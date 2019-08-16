# Django vs Flask
Author:[Marc Steven](https://twitter.com/marcstevencoder)
---

* Django's data models are easier to get started: they are built-in to the framework
* Django assumes that you will use a relational database, if you don't, it will fight you
* Flask allows more flexibility to choose your data model. More Choices mean more potential to screw something up
## Users && Admin
Most dynamic web applications have user accounts
Most people want an admin interface to manage these users
### Compare
#### Users in Django
* django.contrib.auth
* Built-in & easy
* Swapping user model is possible ,but tricky
* Need extra info for users? Make a UserProfile model
### Admin in Django 
* django.contrib.admin
* Built-in $ easy
* High customizable
* Fine-grained permission system
### User in Flask
* Not built-in 
* Most people use "Flask-login" extension: Generic, works with any data model
### User permissions in Flask
* Flask-principal extension provides fine-grained permissions
* Designed to work with or without Flask-Login
*  Similar to Django's user permission system
### Admin in Flask
* Most people use 'Flask-Admin' extension
* Highly customizable Bootstrap themes
* Works with SQLAlchemy, MongoEngine or Peewee
* Designed to work with or without Flask-Login and or Flask-Principal
In my view, the Django suits the large project ,but the Flask can do simple case.


