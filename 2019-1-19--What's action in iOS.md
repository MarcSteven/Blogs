# What's action in iOS
##Action?
As the framework said it's a protocol to deal with event handler
![屏幕快照 2019-01-29 15.33.00](media/15487475035502/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-01-29%2015.33.00.png)

An action is an object that adopts the CAAction protocol.This means simply that it implements runActionForKey:event:object:arguments:).The action object could do anything in response to this message.The notion of an Action is completely general.The only built-in class that adopts CAAction protocol is CAAnimation, but in fact the action object does not have to be an animation- it doesn't even have to perform the animation.
 U would never send run to an object directly.Rather ,this message is sent to an action for you.
 as the basis of implicit animation. The key is the property that was set, and the object is the layer whose property was set.
What an animation does when it receives run(forKey:object:arguments:) is to assume that the object: is a layer, and to add itself to that layer’s animations list. Thus, for an animation, receiving the run(forKey:object:arguments:) message is like being told: “Play yourself!”
This is where the rule comes into play, which I mentioned earlier, that if an animation’s keyPath is nil, the key by which the animation is assigned to a layer’s animations list is used as the keyPath. When an animation is sent run(forKey:object:arguments:),it calls add(_:forKey:) to add itself to the layer’s animation’s list, using the name of the property as the key. The animation’s keyPath for an implicit layer animation is usually nil, so the animation’s keyPath winds up being set to the same key! That is how the property that you set ends up being the property that is animated.
## Action search
When you set a property of a layer ,you trigger the action search:the layer searches for an action object (a CAAction) to which it can send the run(forKey:object:arguments:) message. The procedure by which the layer searches for this object is quite elaborate.
The search for an action object begins when something causes the layer to be sent the action(forKey:) message. Three sorts of event can cause this to happen:
* 1. A CALayer property is set — by calling the setter method explicitly, by setting the property itself, or by means of setValue(_:forKey:). All animatable properties, and indeed most (or all) other built-in CALayer properties, will call action(forKey:) in response to being set.
Setting a layer’s frame property sets its position and bounds and calls action(forKey:) for the "position" and "bounds" keys. Calling a layer’s setAffineTransform(_:) method 

sets its transform and calls action(forKey:) for the "transform" key. You can configure a custom property to call action(forKey:) by designating it as @NSManaged, as I’ll demonstrate later in this chapter.)
* 2,The layer is sent setValue(_:forKey:) with a key that is not a property. This is because CALayer’s setValue(_:forUndefinedKey:), by default, calls action(forKey:).
* 3,Various other miscellaneous types of event take place, such as the layer being added to the interface
At each stage of the action search, the following rules are obeyed regarding what is returned from that stage of the search:
### An action object 
if an action object is produced ,that is the end of search.The action mechanism sends the action object the run(forkey:object:arguments:) message; if this is an animation, the animation responds by adding itself to the layer's animations list.
### NSNull()
If NSNull() is produced ,that is the end of search.There will be no implicit animation;NSNull() means"Do nothing and stop searching."
### nil
if nil is produced, the search continues to the next stage.
The action search proceeds by stages as follows:
*1 ,The layer's action might terminate the search before it even starts.The layer will do this if it's the underlying layer of a view or if the layer is not part of a window's layer hierarchy.In such a case ,there should be no implicit animation, so the whole mechanism is nipped in the bud.(This stage is special in that a returned value of nil ends the search and no animation takes place).
* 2, if the layer has a delegate that implements action(for:forKey:),that message is sent to the delegate ,with this layer as the first parameter and the property name as the key.If an action or NSNull() is returned ,the search ends.
* 3,The layer has a property called actions, which is a dictionary.if there is an entry in this dictionary with the given key,that value is used ,and the search ends.
* The layer looks in the layer’s actions dictionary for a matching key/action pair.
* 4,The layer looks in the style dictionary for an actions dictionary for a matching key/action pair.
* 5 The layer calls the defaultAction(forKey:) class method to look for any class-defined actions.



