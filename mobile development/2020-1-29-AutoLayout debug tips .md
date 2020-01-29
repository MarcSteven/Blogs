# AutoLayout debug tips 
* 1,When you see autoresizing constraints listed in your console output, check your translatesAutoresizingMaskIntoConstraints properties. You may have forgotten to switch this setting off for one or more of your views.
* 2,Constraints must not contradict each other. Two required constraints cannot ask Auto Layout to do inconsistent things at the same time. Try removing constraints or adjusting priorities to resolve the conflict.
* 3,Every logged constraint tells you what it does. The better you understand your logs, the more easily you can identify constraints and connect them to your coding.
* 4,When creating layouts using items instead of formats, rigorously check your math. It’s way too easy to flip the sign of your constant. And, if you can, just use formats.
* 5,Content compression resistance and content hugging are first-class players in Auto Layout. Their priorities can and will overrule layout constraints. Don’t forget to review these values when debugging your layouts.
* 6,When debugging, you must consider all constraints your view participates in, not just the ones installed directly to the view itself. Constraint references may reside in any ancestor view and positioning references will always reside in ancestor views.
* 7,Common visual indications of underconstrained layout include missing views, partially displayed views, and random positions.

