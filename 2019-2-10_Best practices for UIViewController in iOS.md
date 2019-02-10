# Best practices for UIViewController in iOS
* Don't write your animation in the UIViewController.This may be in an independent animation class that accept views to apply it.Special-purpose views may own their own animation.For instance ,a custom spinner controller will have its own animation
* Use dataSource and delegate protocols to separate the code pertaining to data retrieval, update and other business logic.View Controllers are restricted to picking correct view and connecting them to the feeders
* View controllers responder to events from the views -for instance ,button click or table cell selection- connect them back into data sinks
* viewControllers respond to UI -related events from the OS---For instance orientation changes or low-memory warnings, this may trigger relayout of the views
* Don't write custom init code.Because if your storyboard or xib respond to the view Controller ,the init method will never be invoked
* Don't handcraft the UI in the View Controller using Code.Don't implement all the UI ,view creation , and view layout logic in the view controller.Use nibs or storyboard.But if you wanna write the UI via code ,pls make the UI components and then load the subviews into the viewController.
* prefer creating a baseView controller with common setup and have other view controller inherit from this.This technique is not always possible, because there may be a need to inherit from different viewControllers at different parts of the application.For instance ,you should use UITableViewController for the contacts list and uiviewController for the user profile.However if you have multiple places  where you need to display content in a UIWebView, a base ViewController will work well,If you need to display the privacy policy URL or terms and conditions page, you don't need  to subclass. However, if you need to show an image or video that a user shares , You can create subclass that can define custom chrome or control overlays
* Use categories for creating reusable code across view controller.In case a parent view controller doesn't suffice, create categories and add your custom methods or properties there
#Make UIViewController lightweight 
As we all know ,MVC means Massive viewController.So at the most time we always wanna make the UIViewController lightweight.
* 1，remove the dataSource and delegate protocols out of the ViewController
* 2, write the UIView via the code and then custom views into the view and then load all the elements into the viewController
* 3,Remove the network layer out of the UIViewController
* 4, Remove the animation out of the UIViewController  and then write your animation class 
* 5,Remove the logic of transition out of the UIViewController
* 6, choose the MVVM architectural design pattern to remove the logic to the ViewModels.

