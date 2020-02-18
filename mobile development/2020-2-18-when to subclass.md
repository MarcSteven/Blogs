When to subclass in software engineer

Marc Steven

1,Single responsibility In software development, there is a guideline known as the single responsibility principle. This principle states that any class should have a single concern only and it should own all the functionality it uses.
2, Strong types By subclassing, you can creating an additional type. Thanks to Swift's type system, this means you can declare properties or behavior based on objects you know
Shared base classes It's very common to have a shared base class that is subclassed many times by classes that have mutually exclusive behavior:
4 Extensibility Sometimes you simply must subclass if you are extending the behavior of code you don't own.
5,Identity Finally, it’s important to understand that classes and class hierarchies model what objects are. If your goal is to share behavior (what objects can do) between types, more often than not you should prefer protocols over subclassing
