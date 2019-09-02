# 1，考虑UITableViewCell的构造器

```
- (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
What is the purpose of the reuseIdentifier? What is the advantage of setting it to a non-nil value?
```
重用标示符的目的何在？设置它为非空值的时候的优点？
The reuseIdentifier is used to group together similar rows in an UITableView; i.e., rows that differ only in their content, but otherwise have similar layouts.

A UITableView will normally allocate just enough UITableViewCell objects to display the content visible in the table. If reuseIdentifier is set to a non-nil value, then when the table view is scrolled, UITableView will first attempt to reuse an already allocated UITableViewCell with the same reuseIdentifier. If reuseIdentifier has not been set, the UITableView will be forced to allocate new UITableViewCell objects for each new item that scrolls into view, potentially leading to laggy animations.
#2， What are different ways that you can specify the layout of elements in a UIView?

Here are a few common ways to specify the layout of elements in a UIView:

Using InterfaceBuilder, you can add a XIB file to your project, layout elements within it, and then load the XIB in your application code (either automatically, based on naming conventions, or manually). Also, using InterfaceBuilder you can create a storyboard for your application.
You can your own code to use NSLayoutConstraints to have elements in a view arranged by Auto Layout.
You can create CGRects describing the exact coordinates for each element and pass them to UIView’s - (id)initWithFrame:(CGRect)frame method.
# 3， What is the difference between atomic and nonatomic properties? Which is the default for synthesized properties? When would you use one vs. the other?
Properties specified as atomic are guaranteed to always return a fully initialized object. This also happens to be the default state for synthesized properties so, while it’s a good practice to specify atomic to remove the potential for confusion, if you leave it off, your properties will still be atomic. This guarantee of atomic properties comes at a cost to performance, however. If you have a property for which you know that retrieving an uninitialized value is not a risk (e.g. if all access to the property is already synchronized via other means), then setting it to nonatomic can gain you a bit of performance.
#4， Imagine you wanted to record the time that your application was launched, so you created a class that defined a global variable in its header: NSString *startTime;. Then, in the class’ implementation, you set the variable as follows:

+ (void)initialize {
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    [formatter setDateStyle:NSDateFormatterNoStyle];
    [formatter setTimeStyle:NSDateFormatterMediumStyle];
    startTime = [formatter stringFromDate:[NSDate date]];
}
If you then added the following line to the application:didFinishLaunchingWithOptions: method in your AppDelegate:

NSLog(@"Application was launched at: %@", startTime);
what would you expect to be logged in the debugger console? How could you fix this to work as expected?

The debugger console will log Application was launched at: (null) because the global startTime variable has not yet been set. The initialize method of an Objective-C class is only called right before the first message is sent to that class. On the other hand, any load methods defined by Objective-C classes will be called as soon as the class is added to the Objective-C runtime.

There are a couple different ways to solve this problem.

Way to Solve #1: Changing initialize to load will yield the desired result (but be cautious about doing too much in load, as it may increase your application’s load time).

Way to Solve #2, thanks to commenter John, there is another way: create another class method on your created class. Let’s call this new method getStartTime, and all it does is return our global startTime object.

Now we change our NSLog line to:

NSLog(@"Application was launched at: %@", [OurCreatedClass getStartTime]);
Because we’re now sending a message to OurCreatedClass, its initialize will get called , setting startTime. The getStartTime method will then be called, and return the start time!
#5，考虑下面的代码
 
```
#import "TTAppDelegate.h"

@interface TTParent : NSObject

@property (atomic) NSMutableArray *children;

@end

@implementation TTParent
@end

@interface TTChild : NSObject

@property (atomic) TTParent *parent;

@end

@implementation TTChild
@end

@implementation TTAppDelegate

- (BOOL)application:(UIApplication *)application
- didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    TTParent *parent = [[TTParent alloc] init];
    parent.children = [[NSMutableArray alloc] init];
    for (int i = 0; i < 10; i++) {
        TTChild *child = [[TTChild alloc] init];
        child.parent = parent;
        [parent.children addObject:child];
    }
    return YES;
}
@end

```


What is the bug in this code and what is its consequence? How could you fix it?
his is a classic example of a retain cycle. The parent will retain the children array, and the array will retain each TTChild object added to it. Each child object that is created will also retain its parent, so that even after the last external reference to parent is cleared, the retain count on parent will still be greater than zero and it will not be removed.

In order to fix this, the child’s reference back to the parent needs to be declared as a weak reference as follows:

@interface TTChild : NSObject

@property (weak, atomic) TTParent *parent;

@end
A weak reference will not increment the target’s retain count, and will be set to nil when the target is finally destroyed.

Note:

For a more complicated variation on this question, you could consider two peers that keep references to each other in an array. In this case, you will need to substitute NSArray/NSMutableArray with an NSPointerArray declared as:

NSPointerArray *weakRefArray = [[NSPointerArray alloc] initWithOptions: NSPointerFunctionsWeakMemory];
since NSArray normally stores a strong reference to its members.
#6，标识bug所在的那行代码

```
@interface TTWaitController : UIViewController

@property (strong, nonatomic) UILabel *alert;

@end

@implementation TTWaitController

- (void)viewDidLoad
{
    CGRect frame = CGRectMake(20, 200, 200, 20);
    self.alert = [[UILabel alloc] initWithFrame:frame];
    self.alert.text = @"Please wait 10 seconds...";
    self.alert.textColor = [UIColor whiteColor];
    [self.view addSubview:self.alert];

    NSOperationQueue *waitQueue = [[NSOperationQueue alloc] init];
    [waitQueue addOperationWithBlock:^{
        [NSThread sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:10]];
        self.alert.text = @"Thanks!";
    }];
}
@end

@implementation TTAppDelegate

- (BOOL)application:(UIApplication *)application
  didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.rootViewController = [[TTWaitController alloc] init];
    [self.window makeKeyAndVisible];
    return YES;
}

```
如何修正这个问题？

```
When the above code dispatches work using NSOperationQueue’s method addOperationWithBlock, there is no guarantee that the block being enqueued will be executed on the main thread. Notice that the content of the UILabel is being updated within the body of the block. UI updates that are not executed on the main thread can lead to undefined behavior. This code might appear to be working correctly for a long time before anything goes wrong, but UI updates should always happen on the main thread.

The easiest way to fix the potential issue is to change the body of the block so that the update is re-enqueued using the main thread’s queue as follows:

[waitQueue addOperationWithBlock:^{
    [NSThread sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:10]];
    [[NSOperationQueue mainQueue] addOperationWithBlock:^{
        self.alert.text = @"Thanks!";
    }];
}];
```
#7，AppID 和BundleID的区别，每个都用于什么？
An App ID is a two-part string used to identify one or more apps from a single development team. The string consists of a Team ID and a bundle ID search string, with a period (.) separating the two parts. The Team ID is supplied by Apple and is unique to a specific development team, while the bundle ID search string is supplied by teh developer to match either the bundle ID of a single app or a set of bundle IDs for a group of apps.

Because most people think of the App ID as a string, they think it is interchangeable with Bundle ID. It appears this way because once the App ID is created in the Member Center, you only ever use the App ID Prefix which matches the Bundle ID of the Application Bundle.

The bundle ID uniquely defines each App. It is specified in Xcode. A single Xcode project can have multiple Targets and therefore output multiple apps. A common use case for this is an app that has both lite/free and pro/full versions or is branded multiple ways.



By default, any variable that points to another object does so with what is referred to as a “strong” reference. A retain cycle occurs when two or more objects have reciprocal strong references (i.e., strong references to each other). Any such objects will never be destroyed by ARC (iOS’ Automatic Reference Counting). Even if every other object in the application releases ownership of these objects, these objects (and, in turn, any objects that reference them) will continue to exist by virtue of those mutual strong references. This therefore results in a memory leak.

Reciprocal strong references between objects should therefore be avoided to the extent possible. However, when they are necessary, a way to avoid this type of memory leak is to employ weak references. Declaring one of the two references as weak will break the retain cycle and thereby avoid the memory leak.

To decide which of the two references should be weak, think of the objects in the retain cycle as being in a parent-child relationship. In this relationship, the parent should maintain a strong reference (i.e., ownership of) its child, but the child should not maintain maintain a strong reference (i.e., ownership of) its parent.
#8 ，强类型是什么，弱类型是什么，为什么它们是重要的，它们然后用于帮助控制内存管理和消除内存泄露？

For example, if you have Employer and Employee objects, which reference one another, you would most likely want to maintain a strong reference from the Employer to the Employee object, but have a weak reference from the Employee to thr Employer.
#9，Describe managed object context and the functionality that it provides.
A managed object context (represented by an instance of NSManagedObjectContext) is basically a temporary “scratch pad” in an application for a (presumably) related collection of objects. These objects collectively represent an internally consistent view of one or more persistent stores. A single managed object instance exists in one and only one context, but multiple copies of an object can exist in different contexts.

You can think of a managed object context as an intelligent scratch pad. When you fetch objects from a persistent store, you bring temporary copies onto the scratch pad where they form an object graph (or a collection of object graphs). You can then modify those objects however you like. Unless you actually save those changes, though, the persistent store remains unchanged.

Key functionality provided by a managed object context includes:

Life-cycle management. The context provides validation, inverse relationship handling, and undo/redo. Through a context you can retrieve or “fetch” objects from a persistent store, make changes to those objects, and then either discard the changes or commit them back to the persistent store. The context is responsible for watching for changes in its objects and maintains an undo manager so you can have finer-grained control over undo and redo. You can insert new objects and delete ones you have fetched, and commit these modifications to the persistent store.
Notifications. A context posts notifications at various points which can optionally be monitored elsewhere in your application.
Concurrency. Core Data uses thread (or serialized queue) confinement to protect managed objects and managed object contexts. In OS X v10.7 and later and iOS v5.0 and later, when you create a context you can specify the concurrency pattern with which you will use it using initWithConcurrencyType:.
#10， Compare and contrast the different ways of achieving concurrency in OS X and iOS.
There are basically three ways of achieving concurrency in iOS:

threads
dispatch queues
operation queues
The disadvantage of threads is that they relegate the burden of creating a scalable solution to the developer. You have to decide how many threads to create and adjust that number dynamically as conditions change. Also, the application assumes most of the costs associated with creating and maintaining the threads it uses.

OS X and iOS therefore prefer to take an asynchronous design approach to solving the concurrency problem rather than relying on threads.

One of the technologies for starting tasks asynchronously is Grand Central Dispatch (GCD) that relegates thread management down to the system level. All the developer has to do is define the tasks to be executed and add them to the appropriate dispatch queue. GCD takes care of creating the needed threads and scheduling tasks to run on those threads.

All dispatch queues are first-in, first-out (FIFO) data structures, so tasks are always started in the same order that they are added.

An operation queue is the Cocoa equivalent of a concurrent dispatch queue and is implemented by the NSOperationQueue class. Unlike dispatch queues, operation queues are not limited to executing tasks in FIFO order and support the creation of complex execution-order graphs for your tasks.
#11，Will the code below log “areEqual” or “areNotEqual”? Explain your answer.


```
NSString *firstUserName = @"nick";
NSString *secondUserName = @"nick";

if (firstUserName  == secondUserName)
{
  NSLog(@"areEqual");
}
else
{
  NSLog(@"areNotEqual");
```
The code will output “areEqual”.

While one might think this is obvious, it’s not. Here’s why:

Comparing pointer values equates to checking if they point to the same object. Pointers will have the same value if and only if they actually point to the exact same object (whereas pointers to different objects will not have the same value, even if the objects they point to have the same value).

In the above code snippet, firstUserName and secondUserName are each pointers to string objects. One could easily assume that they are pointing to different string objects, despite the fact that the objects that they point to both have the same value. However, the iOS compiler optimizes references to string objects that have the same value (i.e., it reuses them rather than allocating identical string objects redundantly), so both pointers are in fact pointing to same address and the condition therefore evaluates to true.
#12，List and explain the different types of iOS Application States.
The iOS application states are as follows:

Not running state: The app has not been launched or was running but was terminated by the system.
Inactive state: The app is running in the foreground but is currently not receiving events. (It may be executing other code though.) An app usually stays in this state only briefly as it transitions to a different state. The only time it stays inactive for any period of time is when the user locks the screen or the system prompts the user to respond to some event (such as an incoming phone call or SMS message).
Active state: The app is running in the foreground and is receiving events. This is the normal mode for foreground apps.
Background state: The app is in the background and executing code. Most apps enter this state briefly on their way to being suspended. However, an app that requests extra execution time may remain in this state for a period of time. In addition, an app being launched directly into the background enters this state instead of the inactive state.
Suspended state: While suspended, an app remains in memory but does not execute any code. When a low-memory condition occurs, the system may purge suspended apps without notice to make more space for the foreground app.


