# iOS 架构
在iOS中用MVC感觉到怪异？
使用MVVM有疑虑？
听说过VIPER，但是但是不确认它值得
谁应该持有一个网络请求？模型？控制器？
如何将一个模型传递到一个新视图的视图模型中？
谁产生一个新的 VIPER模型？router 或presneter？
##为什么关心选择架构呢？
因为如果你没有，有一天，用大量的不同的事情去调试巨大的类，你将会发现在你的类中你自己不能发现和修正一些缺陷，自然地你将不能作为一个完整的视图去保留在脑海中，另外你总是错过一些很重要的细节。如果你已经和你的应用在这样的场景中，就如同下面一样。
1，这个类是UIViewController的子类
2，你的数据直接保存在ViewController中
3，你的UIView几乎什么也不做
4，你的模型是一个倾斜的数据结构
5，你的单元测试什么也没有覆盖
然后这些发生了，甚至尽管你根据苹果的规范指导和实现了Apple的MVC模式但还是存在。不要为此感到沮丧，使用MVC有一些误区
好的架构有如下特性：
1，在有严格角色的实体之间责任均衡分配
2，可测试性通常来自于第一个特性
3，使用舒适并且维护代价低
##为什么分配
当我们试图指出事情如何工作时，分配在我们的大脑中保持了一个公平的加载。如果你设想你开发更多你的大脑就更好去适应理解复杂性，然后你是正确的。但是这种能力不呈线程扩大并且达到上限非常快，因此最容易的方法去打败复杂是在带有单一责任的多个实体之间划分责任？
##为什么可测试性
这对于已经对单元测试心存感激的人而言不是什么问题。增加一些特性后失败，或者归于重构类的一些复杂的东西而失败。这意味着测试在运行时保存了这些开发者，当一个应用在用户的设备上或许发生，然后修正花了一周到达用户
##为什么可用性
最好的代码是从没有被写的代码，因此你有较少的代码，你的Bug也就少。这意味着你渴望写少量的代码-因此从不单独地解释给一个懒惰的开发者，你一个证实一个明智的方案-闭眼不去考虑维护成本
#MV(X)概要
如今当你来到架构设计的时候，现下有许多可选项
MVC
MVP
MVVM
VIPER
前三个承担将应用的实体放到三个分类中的一个
模型：对于域名数据或者一个数据存取层（操作数据）负责，想起Person 或者PersonDataProvider类
视图：负责展示层（GUI），对于iOS而言，一切以UI开头的任何事都是
控制器/提出者/视图模型：传递者或者粘合剂在视图和模型之间。通常而言，用户呈现在视图的动作作出反应进而更改模型，然后从模型上的变化去更新视图。
分开的视图允许我们做如下事情：
1，更好的理解它们
2，重用它们-大多数可应用到视图和模型上
3，易用性
#MVC  -如何使用
##下面是传统的MVC模式
![屏幕快照 2016-07-04 下午9.38.17](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%889.38.17.png)


在这个时间中，视图是无状态的。一旦模型改变它通过控制器渲染。想想你点击一个链接导航到某个地方，然后网页完全加载。尽管在iOS中实现传统的MVC模式是可能的，但是它没有太多的意义源于这个架构的问题-所有的实体是紧密地耦合，每个视图知道关于其他二个，这戏剧性地减少了它们中的每一个的复用-这不是你想在你应用中有的，对于这个原因我们甚至跳过试图去写一个权威的MVC例子。事实证明，在现代应用中，它似乎难以可应用。
##Apple 的MVC  -异议
![屏幕快照 2016-07-04 下午9.47.33](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%889.47.33.png)

controller充当了调解者-在视图和模型之间。因此它们二者之间对其知之甚少。这就好比做生意一样，一个医疗器械经销商只需要找到下家去卖，而下家则直接联系医院，对于具体下家到底卖给谁，医疗器械经销商不必知道，也没有必要知道，即使想知道也不见得下家会如实告知，这样也减少了麻烦。这最小的重用便是控制器，当然这通常是好的，除非我们有一个地方有不匹配到模型的诡异的业务逻辑。

这虽然很明确，但是有一些问题，或许你也听过人们不压缩MVC,因此它有大量的控制器，这也就是MVC的短板，Massive ViewController，倒真是MVC，只不过多了一分讽刺罢了。因此Controller无法复用，因此这也无可厚非，事物都有二面性，你接受它的优点的同时也得接受它的缺点，不然你就别用它。如果采用传统的MVC，那么它或许会提升一些
##Apple 的MVC -现实
![屏幕快照 2016-07-04 下午10.08.10](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%8810.08.10.png)


Cocoa MVC鼓励你写大量的控制器，因为他们牵涉在视图生命周期内，因此很难去界定独立。尽管你有能力去转让一些业务逻辑和数据传输到模型层，当它转让工作给视图时，你没有更多的选择。**==大多数时间视图的所有责任都是发送动作给控制器==** 而视图控制器以任何事情的代理和数据源来结束，通常职责是派发和取消网络请求。。。。

对于下面的这段代码，我们一定不生疏

```
var customerCell = tableView.dequeueReusableCellWithIdentifier("CustomerOrder") as CustomerCell
userCell.configureWithCustomer(customer)
```
这个单元格是用模型直接在视图内配置的，因此MVC的指导方针是隔离的
但是这一直发生，并且人民并不以为有什么错。如果你严格遵从MVC，然后你认为从controller配置cell，然后不传递模型到视图，这将甚至将递减你控制器的大小
然而，==Cocoa MVC 是合理地不压碎作为大量的视图控制器==
这问题，或许不明显直到单元测试。自从你的视图控制器紧密和视图耦合，它变得难以测试因为你必须非常有创造力在模拟的视图中和他们的声明周期，然而以这样的方式写控制器的代码，你的业务逻辑被尽可能多的从视图布局代码中分离。
让我们看看以下的代码示例

```
import UIKit
struct Person {//Model
let firstName:String
let lastName:String
}
class GreetingViewController:UIViewController {
 var person:Person!
 let showGreetingButton = UIButton()
 let greetingLabel = UILabel()
 override func viewDidLoad() {
 	super.viewDidLoad()
 	self.showGreetingButton.addTarget(self,action:Selector("didTapButton:"),forControlEvent:.TouchUpInside)
 	}
 	func didTapButton(button:UIButton) {
 	let greeting = "Hellow" + "" + "self.person.firstName" +" " + self.person.lastName
 	self.greetingLabel.text = greeting
 	}
 	}
 	//mvc 的集合
 	let model = Person(firstName:"Marc",lastName:"Steven")
 	let view = GreetingViewController()
 	view.person = model
 	


```

*MVC集合能在展示视图控制器中执行*
这似乎可测试性很差，我们能移动greeting生成到一个新的GreetingModel 类中，然后单独测试它。但是我们不能测试任何展示的逻辑-尽管例子中没有太多的这样的逻辑在Greeting视图控制器内（没有直接调用UIView的相关方法，或许会引起加载所有视图，这对于单元测试而言是比较糟糕的）
事实上，加载和测试UIView在一个模拟器上不能保证它在其他设备上工作正常。例如ipad，因此推荐移动Host Application-从你的单元测试目标配置中，然后运行没有应用在模拟器上跑的测试。
**** *视图和控制器之间的交互是真的不可用单元测试测试的*。******************
如上面所说，似乎MVC是一个相当糟糕的模式去选择，但是让我们用特性（定义在文章开始）的术语来估算它。
1，分配-视图和模型事实上是独立的，但是视图和控制器紧密耦合
2，可测试性 -归于糟糕的分配，你将可能仅仅测试你的模型
3，易用性-在其他模式中最小数量的代码。除此之外，其他和它有相似之处，然而它易于维护即使是没有经验的开发者
如果你不打算投资时间在你的架构中，那么MVC是你的选择。如果你认为较高的维护代价是一个过分行为-对于微小的宠物工程。
==*****Cocoa MVC是最好的架构模式在开发速度的团队中。*****==

#MVP -MVC的诺言递送
![屏幕快照 2016-07-04 下午11.10.33](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%8811.10.33.png)

看起来和苹果MVC十分相似，确实如此。它的名字是MVP（被动的视图变体），难道这意味着MVC事实上就是MVP？不是，因为如果你再调用，视图和视图控制器则紧密的耦合在一起，但是MVP的调解者，presenter 不去处理视图控制器的生命周期，视图能被轻易的落空，因此在presenter中一点都没有布局的代码，但是它是对于用数据和状态来更新视图是有责任的。

如果告诉你，UIViewController就是视图
在MVP的选项中，UIViewController子类化事实上是view，而并非presenter，这种差别提供了一流的可测试性，来源于开发速度的花销，因为你必须使手动数据和事件绑定，正如你从例子中看到的一样


```
import UIKit

struct Person {
 	let firstName:String
 	let lastName:String
}
protocol GreetingView:class {
	func setGreeting(greeting:String)
}
protocol GreetingViewPresenter {
	init(view:GreetingView,person:Person)
	func showGreeting()
}
class GreetingPresenter:GreetingViewPresenter {
	unowned let view:GreetingView
	let person:Person
	required init(view:GreetingView,person:Person) {
		self.view = view
		self,person = person
	}
	func showGreeting() {
		let greeting = "Hellow" + "" + self.person.firstName + "" + self.person.lastName
		self,view,.setGreeting(greeting)
		}
	}
	class GreetingViewController:UIViewController,GreetingView {
		var presenter :GreetingViewPresenter!
		let showGreetingButton = UIButton()
		let greetingLabel = UILabel()
		override func viewDidLoad() {
		super.viewDidLoad()
		self.showGreetingButton.addTarget(self,action:Selector("didTapButton"),forControlEvents:.TouchUpInside)
		}
		func setGreeting(greeting:String) {
		self,greetingLabel.text = greeting
		}
		}
		//MVP集合
		let model = Person(firstName:"Marc",lastName:"Steven")
		let view = GreetingViewController()
		let presenter = GreetingPresenter(view:view,person:model)
		view.presenter = presenter
		
```
*****Note******************
MVP是第一个模式 -集中性地披露问题-这之所以发生时归于它有三个事实上独立的层。自从我们不想视图知道模型，它是不对的几种执行在展示视图控制器中-是个视图，然而我们必须在某个地方完成它。例如，我们能使得宽泛的App线路服务-负责几种呈现，-视图到视图的呈现。这个问题产生，必须被定位不仅在MVP中，而且在所有下面的模式中
MVP的特性
分配- 我们有大多数的重任分开在节目支持人和模型之间，用了漂亮的转存视图-例子中模型便是转存
可测试性- 非常优秀，我们能测试大多数业务逻辑归于dump view
易用性-在我们不现实地简单例子中，代码的数量是成倍的-相比较MVC。但是同时MVP的想法很清晰
==MVP在iOS中意味着一流的可测试性但是大量的代码==
MVP -用绑定和喇叭
这里有一个MVP的其他特色-管理控制器MVP。这种变体包含了视图和模型的直接绑定，然而广播支持人-管理控制器   仍然处理从视图来的动作，并且能做改变视图的事情。
![屏幕快照 2016-07-04 下午11.47.56](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%8811.47.56.png)
但是正如之前学的，模糊的职责区分是糟糕的，它和视图和模型紧密耦合一样糟糕，这是类似的事情在cocoa 桌面开发上如何进行。
和传统的MVC一样，在对于一个瑕疵架构而言写一个例子
#MVVM----时下当红的也是最伟大的架构
![屏幕快照 2016-07-04 下午11.51.53](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-04%20%E4%B8%8B%E5%8D%8811.51.53.png)

MVVM是一种最新的架构类型，在10多年之前就已经有人提出，只不过没有人用。和MVC有相似之处，不同的是中间的调解者不同，用ViewModel来充当。
它和MVP有一些相似点
1,MVVM视视图控制器为视图。
2，在视图和模型之间耦合度没有那么紧密了
除此之外，它也绑定像MVP的管理者版本。然而，这次不再视图和模型中，而是在视图和视图模型之间。
在iOS现实中，ViewModel是什么呢？它是基本地你的视图和它的状态的独立呈现。viewModel调用变化在模型中，用更新的模型来更新之间。因此我们在视图和视图模型之间有一个绑定，第一个是相应地被更新
绑定
简明的在MVP中提到过，绑定提出源于OS X 开发的盒子，但是我们没有它们在iOS的工具箱中，当然我们有kvo和通知，但是它们没有绑定那么便利

提供我们不想自己写它们，我们有二个选项：
1，KVO的一种基于绑定库像RZDataBing 或者SwiftBond
2，全放大函数反应式程序像ReactiveCocoa ,RXSwift,PromiseKit

事实上，如今， 你听到MVVM，你想到ReactiveCocoa 和Vice versa 。尽管它可能用简单的绑定来创建MVVM，ReactiveCocoa仍然允许你得到MVVM中的大多数

这里有一点苦涩的真理关于reactive框架 ：伟大的力量源自伟大的责任。它十分容易去和事情混乱（当你运行Reactive）。换句话说，如果你做错事，你或许花很多时间去调试app，因此看看它的回调
在下面的例子中，FRF框架甚至KVO是一个过分行为，相反我们将明确地问ViewModel去更新使用showGreeting方法，然后用对于greetingdidChange回调函数的一个简单属性

```
import UIKit
struct Person {
let firstName:String
let lastName:String
}
protocol GreetingViewModelProtocol:class {
var greeting:String? {get}
var greetingDidChange:((GreetingViewModelProtocol) -> ())? {get set}
init(person:Person)
func showGreeting() 
}
class GreetingViewModel:GreetingViewModelProtocol {
let person:Person
var greeting:String? {
didSet {
self.greetingDidChange>(self)
	}
}
var greetingDidChange:((GreetingViewModelProtocol)->())?
required init(person:Person) {
self,person = person
}
func showGreeting() {
self.greeting = "Hello" + "" + self.person.firstName  + "" + self.person.lastName
	}
}
class GreetingViewController :UIViewController {
 var viewModel:GreetingViewModelProtocol! {
 didSet {
 self.viewModel.greetingDidChange = {[unowned self] viewModel in
 self.greetingLabel.text = viewModel.greeting
 }
 }
 }
 let showGreetingButton = UIButton()
 let greetingLabel = UILabel()
 override func viewDidLoad() {
 super.viewDidLoad()
 self.showGreetingButton.addTarget(self.viewModel,action:Selector("showGretting"),forControlEvents:.TouchUpInside)
 }
 }
 //mvvm集合
 let model = Person(firstName:"Marc",lastName:"Steven")
 let viewModel = GreetingViewModel(person:model)
 let view = GreetingViewController()
 view.viewModel = viewModel
 

```
特性估计
分配：在较小的例子中不明显，但是事实上，MVVM的视图有较多的责任比MVP的视图。因为第一个通过安装绑定的viewmodel来更新它的状态，当第二个向前驱动所有事件到Presenter，它不再自己更新
可测试性：ViewModel对于视图一无所知，这允许我们很容易测试它，视图或许能被测试，但是它是UIKit要依靠的你或许想跳过它
可用性：和MVP一样有相同数量的代码，但是在真实的应用中（你向前驱动所有的事件-从视图到presenter，然后手动更新视图），如果你使用绑定的话MVVM更加瘦身
MVVM是十分有吸引力的，自从他结合了上述方法的福利，此外，他不要求额外的代码给视图去更新归于在视图方面绑定，尽管如此，可测试性仍然有一个高的等级

#VIPER -十分有趣是因为它不是MVX的分类
![屏幕快照 2016-07-05 上午8.55.29](media/14676369145043/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-07-05%20%E4%B8%8A%E5%8D%888.55.29.png)
它有5层
Interactor-包含了涉及数据或者网络的业务逻辑，例如创建视图的新实例 或者从服务器上查找它们。基于这些原因 你将使用一些服务和管理者-不被视为VIPER模型的一部分但是是一个外部的依赖
presenter - 包含了相关业务逻辑的UI，调用方法在interactor上
entities -你 的单一的数据对象，并非数据存取层 业务它是Interactor的职责
Router -负责在VIPER模型之间跳转
基础性地，VIPER模型能是一个屏幕或者你应用的所有用户故事 。
如果对比MVX 会发现一些职责上的差异
1，Model（数据交互）逻辑移入到了带有实体的Interactor内作为数据结构的临时存放处
2，Controller/Presenter/viewModel的UI展示职责移动到了Presenter内，但是不是数据改变能力
3,VIPER是第一个模式-显性地地址导航
因此做线路的适当方法是一个挑战
下面的例子不覆盖在模型之间routing 或者interaction

```
import UIKit
 struct Person {
 let firstName:String
 let lastName:String
 }
 struct GreetingData {
 let greeting:String
 let subject:String 
 }
 protocol GreetingOutput :class {
  fucn receiveGreetingData(greetingData:GreetingData)
  }
  

class GreetingInteractor :GreetingProvider {
	weak var output :GreetingOutput!
	func  provideGreetingData() {
	let person = Person(firstName:"Marc",lastName:"Steven")
	let subject = person.firstName + "" + person.lastName
	let greeting = GreetingData(greeting:"Hello",subject:subject)
	self.output.receiveGreetingData(greeting)
	}
	protocol GreetingView:class {
	func setGreeting(greeting:String)
	}
	class GreetingPresenter:GreetingOutput,GreetingViewEventHandler {
	weak var view:GreetingView!
	var greetingProvider:GreetingProvider!
	func didTapShowGreetingButton() {
	self.greetingProvider.providerGreetingData()
	}
	func receivingGreetingData(greetingData:GreetingData) {
	let greeting = greetingData.greeting + "" + greetingData.subject
	self.view .setGreeting(greeting)
		}
	}
	class GreetingViewController:UIViewController,GreetingView {
	var eventHandler :GreetingViewEventHandler!
	let showGreetingButton = UIButton()
	let greetingLabel = UILabel()
	override func viewDidLoad() {
	super.viewDidLoad()
	self.showGreetingButton.addTarget(self,action:Selector("didTapButton"),forControlEvents:.TouchUpInside)
	}
	func didTapButton(button:UIButton) {
	self.eventHandler.didTapShowGreetingButton()
	}
	func setGreeting(greeting:String) {
	self.greetingLabel.text = greeting
	}
	
	}
	let view = GreetingViewController()
	let presenter = GreetingPresenter()
	let interactor = GreetingInteractor()
	view.eventHandler = presenter
	presenter.view = view
	presenter.greetingProvider = interactor
	interactor.output = presenter
	
```

特性：
分配-毫无疑问它是冠军级别的
可测试性-这里没有什么奇怪的，分配更好则更加有利于测试性能提升
可用性-你必须写大量的接口给类-带有非常小的职责


