# Refactoring table view data source and delegate methods
A lot has been said about the massive view controller problem and how to slim it down. Specifically, you’ve most likely seen how to move the table view data source and delegate methods out of the view controller. The result is fewer lines of code in the view controller.

In this post, we are going to look at some code and different approaches to see if it’s really such a good idea. If not, what should we do instead? Hopefully, you’ll come away with new insights on what you want to do with your own apps.


You can find the source code for this sample project on GitHub.

#Approach 1: Do nothing

You can do nothing. You can just leave the table view data source and delegate methods in the view controller.

Let’s look at a really simple app that lists some ice cream flavors.

The following IceCreamListViewController is very typical. It implements the following UITableViewDataSource methods:

numberOfSectionsInTableView(_:)
tableView(_:numberOfRowsInSection:)
tableView(_:cellForRowAtIndexPath:indexPath:)
The IceCreamListViewController looks like the following if you choose to do nothing:

Swift

class IceCreamListViewController: UITableViewController
{
  let dataStore = IceCreamStore()

  // MARK: - View lifecycle

  override func viewDidLoad()
  {
    super.viewDidLoad()
  }

  // MARK: - Table view data source

  override func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }

  override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }

  override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}


class IceCreamListViewController: UITableViewController
{
  let dataStore = IceCreamStore()
 
  // MARK: - View lifecycle
 
  override func viewDidLoad()
  {
    super.viewDidLoad()
  }
 
  // MARK: - Table view data source
 
  override func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }
 
  override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }
 
  override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}
 
The IceCreamStore is just a very simple Swift class that privately keeps track of some ice cream flavors. It has just one allFlavors() method that returns these flavors. It is there so that you don’t have to store the flavors directly in the view controller.

The following is the full implementation of IceCreamStore:

Swift

class IceCreamStore
{
  private let flavors = ["Vanilla", "Chocolate", "Strawberry", "Coffee", "Cookies & Cream", "Rum Raisins", "Mint Chocolate Chip", "Peanut Butter Cup"]

  func allFlavors() -> [String]
  {
    return flavors
  }
}


class IceCreamStore
{
  private let flavors = ["Vanilla", "Chocolate", "Strawberry", "Coffee", "Cookies & Cream", "Rum Raisins", "Mint Chocolate Chip", "Peanut Butter Cup"]
 
  func allFlavors() -> [String]
  {
    return flavors
  }
}
 
There is nothing wrong with this code. But the three UITableViewDataSource methods reside in the IceCreamListViewController.

Let’s try to move them out of there.

#Approach 2: Refactor the table view data source methods to a separate class

One very common technique that you’ve probably seen many times elsewhere is to move the UITableViewDataSource methods into its own class.

The following IceCreamListDataSource class conforms to the UITableViewDataSource protocol and implements the three methods we’re interested in. The implementation detail is exactly the same. We’re just moving code around, not changing behavior.

Swift

class IceCreamListDataSource: NSObject, UITableViewDataSource
{
  let dataStore = IceCreamStore()

  // MARK: - Table view data source

  func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }

  func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }

  func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}


class IceCreamListDataSource: NSObject, UITableViewDataSource
{
  let dataStore = IceCreamStore()
 
  // MARK: - Table view data source
 
  func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }
 
  func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }
 
  func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}
 
A couple side notes:

IceCreamListDataSource inherits from NSObject so that we don’t have to fiddle with the @objc and @class keywords because UITableViewDataSource is an Objective-C protocol.
The override keyword is only needed for IceCreamListViewController because it is derived from UITableViewController which already conforms to UITableViewDataSource (i.e. already implements these data source methods, albeit maybe empty and meant to be overridden). This means IceCreamListViewController also inherits these data source methods. So you need to override them. But IceCreamListDataSource doesn’t inherit any data source methods so you just need to provide, not override, the implementation.

Now you’re left with a rather empty IceCreamListViewController. You just need to tell the table view to use the new data source by writing tableView.dataSource = dataSource in the viewDidLoad() method.

Swift


```class IceCreamListViewController: UITableViewController
{
  let dataSource = IceCreamListDataSource()

  // MARK: - View lifecycle

  override func viewDidLoad()
  {
    super.viewDidLoad()
    tableView.dataSource = dataSource
  }
}


class IceCreamListViewController: UITableViewController
{
  let dataSource = IceCreamListDataSource()
 
  // MARK: - View lifecycle
 
  override func viewDidLoad()
  {
    super.viewDidLoad()
    tableView.dataSource = dataSource
  }
}
 
Approach 3: Move the table view data source methods to a Swift extension

If the goal is to reduce code in the view controller, what if we just move the data source methods out of the view controller and into a Swift extension.

Swift

class IceCreamListViewController: UITableViewController
{
  // MARK: - View lifecycle

  override func viewDidLoad()
  {
    super.viewDidLoad()
  }
}

// MARK: - Table view data source

extension IceCreamListViewController
{
  var dataStore: IceCreamStore {
    return IceCreamStore()
  }

  override func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }

  override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }

  override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}


class IceCreamListViewController: UITableViewController
{
  // MARK: - View lifecycle
 
  override func viewDidLoad()
  {
    super.viewDidLoad()
  }
}
 
// MARK: - Table view data source
 
extension IceCreamListViewController
{
  var dataStore: IceCreamStore {
    return IceCreamStore()
  }
 
  override func numberOfSectionsInTableView(tableView: UITableView) -> Int
  {
    return 1
  }
 
  override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  {
    return dataStore.allFlavors().count
  }
 
  override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell
  {
    let flavor = dataStore.allFlavors()[indexPath.row]
    let cell = tableView.dequeueReusableCellWithIdentifier("IceCreamListCell", forIndexPath: indexPath)
    cell.textLabel?.text = flavor
    return cell
  }
}

``` 
Once again, the IceCreamListViewController is very empty, and the extension looks identical. The data source method implementation hasn’t changed either.

Note that Swift extension does not allow us to define stored properties. So we have to use a computed property that returns an instance of IceCreamStore(), like it used to before when defined as a constant.

Comparison of the three approaches

Let’s compare the pros and cons of the 3 approaches that you’ve seen based on:

Refactoring
Reusability
Ease of use
Maintainability
Refactoring

The do nothing approach is arguably the easiest, because you don’t have to do anything. But your view controller will increase in size over time because of new features and edge cases. When that happens, you’ll end up with a massive view controller.

Before we dismiss this approach, let’s take a deeper look though. When your view controller increases in size, what kind of stuff gets added. Is it more view controlling logic for your labels, text fields, pickers, and other custom views you may have? Or is it business logic that shouldn’t really be there?

If it’s business logic, you should really move that out of the view controller, instead of the view controlling logic such as table view data source and delegate methods.

If you have a lot of view controlling logic, you then think about how to move them out of there, using either new classes, categories, or extensions.

I think a lot of developers think about solving the massive view controller problem in the opposite direction. To get something working, they put everything in the view controller. When it turns messy, they then think about what easy things they can pull out to slim it down.

I believe we should go about this in the other way. My general rule of thumb is, by default, everything is out of the view controller. When you add code, think about what kind of code you are adding. If it’s business logic, put them in the interactor. If it’s view controlling logic, put them in the view controller.

Instead of default-in-selective-out, it should be default-out-selective-in.

Reusability

The class approach may seem beneficial at first. Your view controller has fewer lines of code. Your new data source class can potentially be reused.

So let’s talk about reusability. The table view data source and delegate methods are very easy to write. How much values does it really add if you spend a lot of time and effort to come up with a generic data source so that you can reuse it in another UITableViewController subclass?

In this simple example, not much. But in a real app, you are likely to have more multiple and/or more complex table views. They may get the data synchronously and asynchronously. They may format the data differently. They may have different section and row logic. Some sections may need to be hidden and some rows are need to be dynamic. Who knows what the UI/UX designers can come up with in their dreams?

If you were to make this generic data source reusable for all these situations, it can very well turn into a massive data source itself! It’ll be harder and harder to read and understand, making it more difficult to use by all your UITableViewController subclasses, by other developers on your team who are less familiar with the data source.

If it’s easier to write them out every time than to make a generic data source class, why do we care so much about making it reusable? On the contrary, is DRY really that important if the data source code is trivial to write? Which one is easier to use? Which one do most developers already know very well?

I’ll take readability over reusability.

Ease of use

When it comes to the effort you make to create this generic data source class. Does it take more time to do nothing or do something? It’s obvious in the short term. But over the long term, you better do something now before your view controller becomes massive. That’s why you are reading, right?

So, after throwing the do nothing approach out the window, should we use the class approach or the extension approach?

The class approach requires you to manually set the table view data source to your new custom data source. If you forget, weird things happen. You also need to make sure all developers on your team know about this data source, know how to use it, know to set it in viewDidLoad(). BTW, don’t you already have tons of stuff in viewDidLoad()? Your new data source class also needs to inherit from NSObject or use the @objc keyword. That’s just fact of life for Objective-C interoperability. Oh, you also need to get rid of that override keyword?

The extension approach is just straight copy-and-paste. Yes, you do need to make your data source a computed property because Swift extension does not allow stored properties. Mind you, Objective-C category does not allow stored properties either.

I’ll take extension over class.

Maintainability

In a real app, your custom data source class is likely to be more complex than what I’ve shown above, as we discussed about reusability. When you need to make the data source work for one more view controller, it’s going to be harder to add this new feature. Or, when the data source has bugs, it’s also going to be harder to fix.

But if you do nothing or use an extension, even though it’s not as DRY, it’s going to be easy to modify. After all, it’s not meant to be reused. It only needs to work for this one view controller. It doesn’t need to work for all your view controllers in your app, or even across other apps.

Suppose you don’t build this custom data source on your own. And you use a popular data source solution from GitHub. Does the author have time to fix issues, merge pull requests, and provide support? What if the data source break when new versions of Xcode and Swift come out? How soon will it get back working again? Can you afford to wait?

Free software is free software. You aren’t paying the author to do all this free work. Suppose the author is so gracious and do a reasonable amount of support. What if he changes his mind and loses interest in further maintain the data source repo? He may move on to other more interesting and rewarding projects.

Apple, as a company, is much more likely to maintain UITableViewDataSource and UITableViewDelegate protocols as long as the iOS platform is not deprecated. When new versions of Xcode and Swift come out, they are guaranteed not to be broken.

Apple’s support lasts longer than any individual’s.

Conclusion

I found refactoring table view data source and delegate methods from the view controller into a separate class is just not that useful. It requires more effort. Reusability is overrated. A super generic, reusable data source class is harder to get it right and maintain.

Often, it’s very easy for us developers to get overly creative and fall into the trap of making something cool. But it’s also important to be pragmatic when we refactor.

My preferred solution is:

For a simple view controller, where there is only one table view, leaving the data source and delegate methods right in the view controller isn’t bad.
For a complex view controller, where there are maybe, 4 or 5, sibling or nested, table views, separating each into its own Swift extension or Objective-C category may often be enough.
If you truly have a need for a generic table view data source, I recommend watching the WWDC 2014 – Advanced User Interfaces with Collection Views session. Just make sure the time-vs-benefit tradeoff is worthwhile.
The key is to move all business logic out of the view controller and into the interactor while keeping the view controlling logic in the view controller. I believe that is more important in slimming down a massive view controller than refactoring out table view data source and delegate methods.

The source code used in this post is on GitHub.

