# navigator in iOS
author:[@MarcSteven](https://twitter.com/marcstevencoder)
---
in iOS ,there're the basic data flow from ScreenA to Screen B.
Your ViewController A perhaps has something like this :

```
override func tableView(_ tableView:UITableView,didSelectRowAt indexPath:IndexPath) {
let detailsData = tableData[indexPath.row]
let detailViewController = ViewControllerB()
detailViewController.data = detailData
navigationController?.pushViewController(detailViewController,animated:true)
}
```
Your viewcontrollerB probably has somthing like this :

```
init() {

navigationItem:rightBarButtonItem = UIBarButtonItem(title:"Go to ViewController c!",style:.plain,target:self,action:#selector(didTapGoButton))
}
func didTapGoButton() {
let followingViewController = ViewControllerC()
navigationController?.pushViewController(nextVC,animated:true)
}
```

But what's the wrong with it ?
Standard action most developer wrote this code per day.

The role of ViewController:
1,parent
2,child
3,boss

In ViewControllerA there are lots of job to do .
What are the problems?
ViewController are not independent
viewController depend n having parents that can do certains things 
ViewController are doing too much (flow,presentation ,and so on)
Solution v1:Delegation
![]()
Use delegation to have a master viewController to do all 

##How does it work?
###1,Each viewController taht requires navigation has a delegate
###2, Our rootViewController is MasterNavigationViewController:UINavigationController
###3,MasterNavigationViewController configures every vc and is its delegate

## what odes this fix?
###1,individual viewControllers don't control flow/presentation
###2,individual viewController don't need to know about their parents
###3 ,individual viewController don't create other non-child viewControllers.
## what isnot so great?
###1,doing all our magic in viewController
###2,code duplication and or implicit dependencies between delegates
###3, generally lots of boilerplate,everything has a navigation delegate!
Note:MasterNavigationViewController is still a viewController
1,As the app grows ,this top level viewController will likely house other logic control logic.
2,Still tied to all the viewController lifetime magic of UIKit .
3,The ViewController delegate can easily get bloated with all kinds of tasks ,not just nav.
So much boilerplate
##1,Every VC that can navigate must now have a delegate
##2,Boilerplate grows as a function of how much navigation is possible 
##solution v2:Navigator
###What's so great about the Navigator?
####1,A standalone object
####2,Not tied to viewController life cycle or responsibilites
####3,we can use navigator protocols to not have to repeat boilerplate
####4,.....And to group functionality
### how does it work?
####1,Navigator is the base protocol
####2,all navigator protocols conform to Navigator
####3,Navigator protocols are grouped by functionality ,not per ViewController



