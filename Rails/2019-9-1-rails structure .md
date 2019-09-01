# rails structure 



| File/Folder | Purpose | BestPractise |
| --- | --- | --- |
| app/ | contains the controllers, models ,helpers, mailers,channels,jobs and assets for your application. | *Cell:App cells are simple ruby classes that can render templates. *decorators:App decorators is a design pattern to remove view methods from models.
*forms:Form Object is a design pattern that encapsulates logic related to validating and persisting data. Using these can improve the way you implement complex forms.
*inputs:Simple form allows to create custom input components and you can place them here.

*performers:Performers is another design pattern to abstract view methods from the model using modules.*Policies:Policies are plain old ruby objects that handle presentation logic.*Presenters:Presenters is another design pattern to abstract view methods from the model using PORO.
*Services:Contains app services. A service object implements the user’s interactions with the application. It contains business logic that describe the connections with your domain objects.
*user_cases:Use Cases is pretty much the same thing as services. They are designed to break up non-trivial business logic.
*app/uploaders:A uploader is a class that is used by CarrierWave gem to model an uploaded file.

*values:Value objects are an abstraction where equality is based on internal fields instead of identity.

*workers:Workers are objects that allow you to run processes in the background. Remember, it is recommended to use active job instead of your own workers so you can later switch out job runners without having to worry about api differences. |

* bin - Contains the rails script that starts your app and can contain other scripts you use to set up, update ,deploy or run your application.
* Config - Configure your application's routers ,database, and more.
* db- contains your current database schema, as well as the database migrations.
* lib- extended modules for your application
* log- application log files
* public - the only folder seen by the world as-is. Contains static files and compiled assets
* Test- Unit tests, fixtures, and other test apparatus
* spec - alternative to test directory using BDD. Rspec allows you to write an alternative syntax to Test unit that reads more like a specification than a test
* tmp- temporary files (like cache and pid files)
* Vendor - a place for all third-party code. In  


