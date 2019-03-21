# Angular architecture best practices 


* Consider using the Mono-Repo style of development when building large applications and working with multiple development teams.
* Think about how you will modularize your application into specific feature-sets and build your modules to encapsulate those features.
* Follow the separation of concerns principles.
* Create abstraction layers in your application; they help to define what each part of the application should be doing and help with code organization.
* Create your interfaces so that they can be used globally throughout your application.
* When necessary, use state management libraries to manage state in your application. I would recommend using NgRx or Nrwl.
* Create smart components in your controller layer.
* Use dumb components for your view or presentation layer.
* Pay attention to the change detection strategy used in your components.





