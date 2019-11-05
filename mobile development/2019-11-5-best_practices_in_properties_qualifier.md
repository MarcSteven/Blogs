#Tips
* 1,For scalar properties(NSInteger,SEL,CGFloat,etc), use the *assion* qualifier
* 2, for block properties, use the copy qualifier. 
Why?**You should specify copy as the property attribute, because a block needs to be copied to keep track of its captured state outside of the original scope. This isn’t something you need to worry about when using Automatic Reference Counting, as it will happen automatically, but it’s best practice for the property attribute to show the resultant behavior.** 
* 3, When declaring methods with NSError ** parameters, use __autoreleasing with the correct synatax:
NSError* __autoreleasing *.
* 4, Avoid directly referencing outer variables in a block. ** weakify them outside the block and then strongify them inside the block.
But why scalar properties should use assion? Based on the apple document it always describe like that -only one sentence.
