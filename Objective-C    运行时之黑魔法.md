# Objective-C    运行时之黑魔法
OC本身就是一个对话，它的关于消息传送和活力的所有都没有什么，只是没有一个运行时的大话的分支去做难的事情
几乎不公平 OC语言得到所有信用，当它是真正的OC运行时的事情发生
你的面向对象的范例是通过微生物的交互鼓舞，OC中的运行时作为一种变化操作系统，促进数据结构和函数调用-实现OC的动态特性
每一个类声明，方法调用和表达式求值被编译进入相同的C函数去用运行时去交互，事实上一个开发者的大多数的交互都是通过OC的运行时。但是作为一个C的严格的超集，这些运行时函数也能被直接调用。
#libobjc
这是OC对于Objective-C 2.0 运行时的共享库。它能被直接使用从一个OC应用到自省和改变它本身的行为
OC开发者处于一个遵从这个不详的咒语的警惕的境界中，一个好的原因是把OC的运行时弄乱去改变对于运行在它上面的代码的实体的结构。
#消息发送
在OC面向对象范例的核心是消息传送的概念-它被珍藏在方括号内的语法中去界定发送消息给一个对象的行为
考虑下面的OC代码
[object message];
编译器将翻译它进入一个相等的调用中
 obj _msgSend(object,@selector(message));
一个类维护一个派发表格去解决消息在运行时发送；表格中的每个实体是一个方法-键入一个通常的名字，方法选择器。对应一个实现-是一个指针到一个底层的C函数
当消息被发送给对象时，它咨询它的类的派发表格去查找与消息的方法选择器关联的实现。如果匹配找到，关联的函数被调用。如果没有发现，超累的派发表格被咨询，等等，知道要么一个匹配被查找到，要么方法选择器被决定是不识别的。
用动态派发，基于代码的消息转发行为是在运行时不能决定的。执行的每一个分都被推迟直到最后一个可能的瞬间。对象的类能被更改，响应给消息选择器的方法有自己的实现替代，或者对象的ivar布局能以崩溃在进程中的方式蓄意破坏。一切皆有可能
这样的方式是冒险和窃取OC运行时的报酬
它无所获并且通过调用objc_msgSend直接失去一切。OC的代码难以置信地有一个负责的编译器和分析器-它能察觉和防止不正确的行为。剪掉中间人意味着直接承当所有责任，没有直接受益。
但是，如果你想活在危险中，并且考虑愚蠢的开发练习去获得资格
objc_msgSend:用一个简单的返回值发送一个消息
objc_msgSend_stret:用一个数据结构简单值给一个类发送一个消息
objc_msgSendSuper:用一个简单返回值作为消息给一个类的超类
objc_msgSendSuper_stret:用一个简单数据结构简单值作为消息给一个列的超类
#用属性的宏大的程序
属性给一个对象的状态定义了公共接口，使用libobjc，一个聪明的开发者能在一个完全地新的等级上协同类进行工作
例如 使用去存取一个对象属性的列表，手动实现NSCoding的苦差事能用一系列宏大的程序消除
#pragma mark -NSCoding
	⁃	(id)initWithCoder:(NSCoder *)decoder {
	 	self =  [super init];
	 	if (!self) {
	 	return self;
	 	}
	 	unsigned init count;
	 	objc_property_t *properties = class_copyPropertyList([self class],&count);
	 	for (NSUInteger i = 0;i < count; i++) {
	 	objc_property_t property = properties[i];
	 	NSString *key = [NSString stringWithUTF8String:property_getName(property)];
	 	[self setValue:[decoder decodeObjectForKey:key] forKey:Key];
	 	}
	 	free(properties);
	 	return self;
	 	}
	 	-(void)encodeWithCoder:(NSCoder *)coder {
	 	unsigned int count;
	 	objc_property_t *properties = class _copyPropertyList([self class],&count];
	 	for (NSUInteger i = 0 ;i < count;i ++) {
	 	objc_property_t_property= properties[i];
	 	NSString * key = [NSString stringWithUTF8String:property_getName(property)];
	 	free(properties);
	 	}
	 	
	 		


#关联对象
关联对象是在语言本身中没有一个副本的OC运行时特性。
这个特性允许对象在运行时去关联任意值给键，它能常常用于工作在常量周围-阻止分类去声明新的存储属性

```
“@interface NSObject (AssociatedObject)
@property (nonatomic, strong) id associatedObject;
@end

@implementation NSObject (AssociatedObject)
@dynamic associatedObject;

“- (void)setAssociatedObject:(id)object {
     objc_setAssociatedObject(self, @selector(associatedObject), object, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (id)associatedObject {
    return objc_getAssociatedObject(self, @selector(associatedObject));
} 
```
关联对象是相当简单：用 objc_setAssociatedObject去存取一个关联之，然后使用objc_getAssociatedObject 去重新得到它
每一个关联对象都通过一个键来提及，键可以是任意的常量值。早期的解决方案是床底getter方法的一个方法选择器
#动态增加一个方法
仅仅作为一个属性去描述一个对象的状态，方法包含它们的行为，在OC中，方法用+/-开头来声明，去指示一个方法是用一个雷关联或者那个类的实例来关联
通常地，类声明是十分稳定的。留出扩展和分类的混合效果，类的方法通常通过一个App的执行维持稳定。然而使用OC运行时，方法能被如期望一样增加移除
考虑下面的分类

```
“@interface NSObject ()
- (NSString *)greetingWithName:(NSString *)name;
@end

@implementation NSObject ()
- (NSString *)greetingWithName:(NSString *)name {
  return [NSString stringWithFormat:@"Hello, %@!", name];
}
@end
```
通过在编译时在源文件中引入这个代码，greetingWithName将是可用的，对于NSObject的所有实例，然而，这个对等的行为相反地在运行时中也可完成

```
Class c = [NSObject class];
IMP greetingIMP = imp_implementationWithBlock((NSString *) ^(id self,NSString  *name) {
	return [NSString stringWithFormat:@"Hello, %@ !",name];
	});
const char *greetingTypes = [NSString stringWithFormat:@"%s%s%s",@encode(id),@encode(id),@encode(SEL)] UTF8String];
class_addMethod(c,@selector(greetingWithName:),greetingIMP,GreetingTypes);

```
 这个增加一个新的方法和属性的能力使得它更加可能去在基于DSLs的宏项中定义负责的行为，或是在一个SDK内部去解决兼容的缺陷，这将是以一个合理的价格做了更为强大的事情，却是事半功倍
  

#方法混🈴
方法混用是一个改变一个已经存在的方法选择器的过程。这是一个技术-方法调用在OC运行时中能被修改的事实使得成为可能，通过改变方法选择器被映射到一个雷的派发表格中的底层函数中
考虑到追踪任务-在视图控制器的生命周期内，每个视图控制器被呈现多次。
每个视图控制器能增加追踪代码到ViewDidAppear的实现的一个重写实现中，但是这将造成数以万计的弃用代码。子类化是另外一个可能，但是它要求UIViewController，UITableViewController,UINavigationControll子类化，并且每个其他的视图控制器类-一个方法（忍受过多的代码弃用）
用方法混用，这个方案十分高雅

```
#import<objc/runtime.h>
@implementation UIViewController(Tracking)
+ (void)load {
static dispatch_once_t onceToken;
dispatch_once(&onceToken,^{
Class class = [self class];
SEL originalSelector = @selector(viewWillAppear:);
SEL swizzledSelector = @selector(xxx_viewWillAppear:);
Method originalMethod = class _getInstanceMethod(class,originalSelector);
Method swizzledMethod = class_getInstanceMethod(class.swizzledSelector);
BOOL didAddMethod = 
class_addMethod(class,originalSelector,method_getImplementation(swizzledMethod),method_getTypeEncoding(swizzledMethod));
if (didAddMethod) {
class_replaceMethod(class,swizzledSelector,method_getImplementation(originalMethod),method_getTypeEncoding(originalMethod));
} else {
method_exchangeImplementations(originalMethod,swizzledMethod);
}

});
}
#pragma mark -method swizzling
- (void) swizzled_viewWillAppear:(Bool)animated {
[self swizzled_viewWillAppear:animated];
NSLog(@"viewWillAppear: %@",self);
}
@end
```
现在，当UIView的任意实例或它超类中的一个调用viewwillAppear，一个日记陈述句将被打印
因为方法混合影响了全局状态，它是重要的使得竞态条件的可能性减少。+load是在类初始化期间被执行的保障，因此对于改变系统广泛的行为一点点的相容性。通过对比，当它将被执行时。初始化无此保证
注入行为到一个视图控制器的生命周期，响应事件，视图绘制，或者foundation网络栈是一个方法混用有很好效果的最佳例子。这有大量的其他场合当混用将是适当的技术，它使得OC开发者变得越来越老练


#动态的创建一个类
结合动态属性和方法定义的之前的概念，libobjc的最终功绩是 在运行时中生成一个类
考虑下面的代码

```
@interface Product:NSObject
@property(readonly) NSString * name
@property(readonly) double price;
- (instancetype)initWithName:(NSString *)name price:(double)prince;
@end


@implementation Product
 -(instancetype)initWithName: (NSString *) name
 					price :(double) price {
 				self = [super init];
 				if(!self) {
 				return nil;
 				}
 			self.name = name;
 			self.price = price;
 			return self;
 			}
 			@end
```
如果这里有一个sympathetic去生成，代码会更加高雅对比它的运行时等值


```
“Class c = objc_allocateClassPair([NSObject class], "Product", 0);
class_addIvar(c, "name", sizeof(id), log2(sizeof(id)), @encode(id));
class_addIvar(c, "price", sizeof(double), sizeof(double), @encode(double));

Ivar nameIvar = class_getInstanceVariable(c, "name");
ptrdiff_t priceIvarOffset =
    ivar_getOffset(class_getInstanceVariable(c, "price"));

IMP initIMP = imp_implementationWithBlock(
    ^(id self, NSString *name, double price)
{
    object_setIvar(self, nameIvar, name);
“char *ptr = ((char *)(__bridge void *)self) + priceIvarOffset;
    memcpy(ptr, &price, sizeof(price));

    return self;
});
const char *initTypes = [[NSString stringWithFormat:@"%s%s%s%s%s%s",
    @encode(id), @encode(id), @encode(SEL), @encode(id), @encode(id),
    @encode(NSUInteger)] UTF8String];
class_addMethod(c,
                @selector(initWithFirstName:lastName:age:),
                initIMP,
                initTypes);

IMP nameIMP = imp_implementationWithBlock(^(id self) {
    return object_getIvar(self, nameIvar);
});
const char *nameTypes =
    [[NSString stringWithFormat:@"%s%s%s",“@encode(id), @encode(id), @encode(SEL)] UTF8String];
class_addMethod(c, @selector(name), nameIMP, nameTypes);

IMP priceIMP = imp_implementationWithBlock(^(id self) {
    char *ptr = ((char *)(__bridge void *)self) + priceIvarOffset;
    double price;
    memcpy(&price, ptr, sizeof(price));

    return price;
});
const char *priceTypes = [[NSString stringWithFormat:@"%s%s%s", @encode(double), @encode(id), @encode(SEL)] UTF8String];
class_addMethod(c, @selector(age), priceIMP, priceTypes);

objc_registerClassPair(c);
```

让我们来瞅瞅这上面到底做了什么
首先一个类用objc _allocateClassPair分配，配置了类的超类和名字，然后用class_addIvar将实例变量增加到类中，这里第四个参数通常用来决定最低限度呈线性，它依赖于ivar的类型和目标平台结构，对于任意指针类型的变量而言，正确的alignment是log2（sizeof(type)）.
 下一步是定义初始化器的实现-imp_implementationWithBlock,去设置name，简单调用object_setIvar。通过在先前计算的抵消内执行一个memcpy设置。
 为了增加一个初始化器，每一个参数的类型编码都需要被计算。这是编码的一个尴尬的困境，字符串插入，但是它完成了这项工作。
 给ivar的getter增加方法遵从了相同的大多数进展

为什么类要配对？每个类有一个多类，管理对于消息发送的派发表格到类本身，在OC中类是对象，类方法事实上对于类对象而言是实例方法。

最后一旦所有方法被增加，类在运行时被注册。


