# What's Self and self in swift 
When we are writing protocol and protocol extensions ,there is a difference between Self and self.
When used with self.it refers to the value inside that type, but when you used with Self, Self refers to the type that conform to the protocol
self refers to the current instance ,in the body of one of its method .
Self is a placeholder used in two different cases:
* 1, In a protocol ,it refers to the type that conform to the protocol in any particular use,In Equatable ,for instance ,it's used to required that the two values being compared are of the same type.It something like a generic type parameter that you don't have to put between the <...> because it's deduced from the context of its usage.
* 2,In a class /static method ,it can be used as the return type to indicate that the return type is the type of the class to which the method was sent, rather than the class in which the method is declared, it's similar to the instancetype in Objective-C.



