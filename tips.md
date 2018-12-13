# tips
#   1，使用IBDesignable/IBInspectable进行圆角化设置
很多场景下我们都需要进行圆角设置，例如Button，View，ImageView等，做法多种，我们可以用代码实现，但是每次都难免要写这样的代码

```
//Swift version
doneButton.layer.cornerRadius = 4.0f
doneButton.layer.maskToBounds = true
//OC 版本
self.doneButton.layer.cornerRadius = 4.0f;
self.doneButton.layer.maskToBounds = YES;
```
当许多页面需要圆角化的 时候，我们要写很多重复这样的代码。
采用IBDesignable/IBInspectable的做法
1,新建一个swift类

```

 import UIKit
  @IBDesignable  class CustomView: UIView {
    @IBInspectable var cornerRadius: CGFloat = 0 {
        didSet {
            layer.cornerRadius = cornerRadius
            layer.masksToBounds = cornerRadius > 0
        }
    }
    @IBInspectable var borderWidth: CGFloat = 0 {
        didSet {
            layer.borderWidth = borderWidth
        }
    }
    @IBInspectable var borderColor: UIColor? {
        didSet {
            layer.borderColor = borderColor?.cgColor
        }
    }
}


```
2,在IB上托一个View，选择 show indentify inspector中的Custom Class，设为CustomView，然后依照对应的属性进行设置

![屏幕快照 2017-06-04 下午7.54.43](media/14965764087301/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-06-04%20%E4%B8%8B%E5%8D%887.54.43.png)


#2,如何从模型传数据到controller
无论使用MVC,或者MVVM模式，你所有的请求接收和解析数据都是围绕更新UI来进行
1，使用回调
2，使用delegate
3，使用通知
在任何场景下，我们在数据模型中得到数据后，需要一种方式将其传送到对应的VC中
例如：此处有二个类：VC和DataModel

```
class ViewController:UIViewController {
}
class DataModel {
}

```
## 2.1.1 回调作为完成句柄，相当于一个block

```
class DataModel {
func requestData(completionHandle:((_ data:String)->Void)) {
// 数据获取并解析到string
let data = "Data from Wherever"
}
}
//completionHandle是一个方法，带一个String作为Data，有一个Void的返回类型
```
我们需要做的就是用我们接受的数据去调用completionHandle

```
class DataModel {
   func requestData(completion: ((data: String) -> Void)) {
      // 数据获取解析到string
      let data = "Data from wherever"
      completion(data)
   }
}
```
下一步就是在VC类中创建一个DataModel的实例，然后调用RequestData方法

```
class ViewController: UIViewController {
   private let dataModel = DataModel()
   override func viewDidLoad() {
      super.viewDidLoad()
      dataModel.requestData { [weak self] (data: String) in
            self?.useData(data: data)
      }
   }
   private func useData(data: String) {
       print(data)
   } 
}
```
这样一来我们的VC中有了数据
##2.1.2 回调作为类属性
另外一种方式就是创建一个回调作为DataModel的一个属性
```
class DataModel {
      var onDataUpdate: ((_ data: String) -> Void)?
}
```
现在在数据请求方法内替代使用完成句柄，使用这个回调

```
func requestData() {
let data = "Data from server"
onDataUpdate?(data)
}

```
在VC中使用这个回调，我们仅仅简单地赋一个合适的方法给它

```
class ViewController:UIViewController {
private let dataModel = DataModel()
override func viewDidLoad() {
super.viewDidLoad() 
dataModel.onDataUpdate = {[weak self] (data:String) in
self?.useData(data:data)
}
dataModel.requestData()
}
}
// 在闭包中如果直接使用self。会出现循环引用，因此使用weak self或者无主引用来消除循环引用。

```
## 2.2 使用delegate
很常用的方式来在VC和model之间交互

```
protocol DataModelDelegate:class {
func didReceiveDatUpdate(_ data:String)
}
```
在swift协议定义中，类关键字限制协议去遵循于类，而不是结构和枚举，结构和枚举属于值类型，而类是引用类型，如果你想使用一个弱引用给delegate。这样不会造成循环引用

现在我们创建了一个弱的delegate在DataModel中

```
weak var delegate:DataModelDelegate?

```
调用回调我们依然和回调方法一样使用

```
class DataModel {
weak var delegate:DataModelDelegate?
func requestData() {
let data = "Data from server"
delegate?.didRecieveDataUpdate(data:data)
}
}

```
vc中创建一个DataModel的实例，赋一个Delegate给self，然后执行requestData方法

```
class ViewController: UIViewController {
      private let dataModel = DataModel()
      override func viewDidLoad() {
         super.viewDidLoad()
         dataModel.delegate = self
         dataModel.requestData()
      }
}
```
最后一步，生成VC的扩展，并且遵从DataModelDelegate协议，使用协议内的delegate方法

```
extension ViewController: DataModelDelegate {
      func didRecieveDataUpdate(data: String) {
         print(data)
      }
}
```
回调和delegate比较而言，delegate模式更容易重用，你能创建一个积累遵从协议来消除多余的代码，但是delegate更难实现，你需要创建一个protocol，设置protocol方法，创建delegate属性，赋值delegate给VC，使VC遵从protocol。Delegate必须实现protocol的每个方法（默认）
##2.3 通知
 当前二个方法普遍使用的时候，通知是不明显的。以下是一个可能使用退出的场景去在dataModel和VC之间交互
 我们有一个共享的数据源，你想通过App去使用它。例如你需要获取大量本地储存的用户图片，，在多个VC中舒勇它，使用delegation将要求每个VC都必须遵从protocol。
 1，我们使得它singleton class
 
```
class DataModel {
static var sharedInstance = DataModel() 
private init() {}
}

```
2,增加一个局部变量到DataModel中，将存储我们的数据

```
class DataModel {
   static var sharedInstance = DataModel()
   private init() { }
   
   private (set) var data: String?//因为只想找个属性为可读，所以使用了private(set) 存储修饰符，仅有的修改这个属性是通过requestData方法
}
```
3，最后我们实行同样的requestData方法

```
class DataModel {
   static var sharedInstance = DataModel()
   private init() { }
   
   private (set) var data: String?
   func requestData() {

   }
}
```
一旦我们在requestData得到数据，我们在局部变量内存取它

```
func requestData() {
   // the data was received and parsed to String
   self.data = “Data from wherever”
}
```
我们更新了局部变量后，我们想要发一个通知，最好的方式去做这个就是使用属性观察者，增加didSet属性观察者给数据变量

```
private (set) var data: String? {
   didSet {
      
   }
}
```
在发送通知之前，我们需要取一个有一个的名字

```
let dataModelDidUpdateNotification = “dataModelDidUpdateNotification”

```
现在我们准备发一个通知

```
private (set) var data: String? {
   didSet {
      NotificationCenter.default.post(name:  
NSNotification.Name(rawValue: dataModelDidUpdateNotification), object: nil)
   }
}

```
属性观察者将在变量数据内观察任何变化，当这些变化发生的时候，我们发了一个通知，此刻我们需要增加一个倾听者给这个通知在每个VC中需要用到这个数据

```
class ViewController: UIViewController {
     override func viewDidLoad() {
         super.viewDidLoad()
         NotificationCenter.default.addObserver(self, selector: #selector(getDataUpdate), name: NSNotification.Name(rawValue: dataModelDidUpdateNotification), object: nil)
     }
} 
```
此处没有创建DataModel的局部实例，我们通过DataModel.sharedInstance.data属性来做
当处理通知的时候，我们需要记住一个事情，就是当它不需要监听通知的时候，需要移除观察者

```
deinit {
      NotificationCenter.default.removeObserver(self, name: NSNotification.Name(rawValue: dataModelDidUpdateNotification), object: self)
}
```
在一些场景中，如果VC仍然在导航栈中但是目前不可见的时候，你不需要一个观察者去监听通知。例如，我们在第一个VC的顶部呈现第二个VC，更新底部的一个浪费资源，在这个时间中，我们增加一个观察者在viewWillAppear中，在viewWillDisappera中移除它。
最后一步，就是调用request方法

```
class View Controller: UIViewController {
   override func viewDidLoad() {
        super.viewDidLoad()
         NotificationCenter.default.addObserver(self, selector: #selector(getDataUpdate), name: NSNotification.Name(rawValue: dataModelDidUpdateNotification), object: nil)
        DataModel.sharedInstance.requestData() 
    }
## }
```

