# import statement  in Python
Author:[Marc Steven](https://twitter.com/marcstevencoder)
---
As we all know, python has two different versions of the import statement:
* 1, Importing a module and then using the module name to access something defined within that module. For example
* `import math
	print(math.pi)
	
	
`
* 2 , Import something from module and using that thing directly.
'from math import pi
print(pi)
'
The import statement is very powerful, however, and we can do all sorts of interesting things with it.
What does the import statement actually do?
Whenever you create a global variable or function, the Python interpreter adds the name of that variable or function to what is called the global namespace.The global namespace holds all the names that you have defined at the global level
When something is in the global namespaces ,you can access it by name from anywhere in your program.
But in Python there is a second namespace ,called the local namespace,-that holds variables and other things  defined within the current function. While the local namespace is important when it comes variable scope.
# Using the import statement
* import <something>
* from <somewhere> import <something>
with the first form ,you are not limited to import modules one at a time, you can import multiple modules at once like that
'import string, math, datetime,random'
Similarly, you can import multiple things at once from a module or package
'from math import pi, radians, sin'
If you have more items to import than will fit on one line, you can either use line continuation characters(\) to spread the import across multiple lines ,or surround the list of items that you want to import with parentheses, for example

'from math import pi, degrees, radians, sin, cos, \
					  tan,hypot
					  
 from math import (pi, degrees, radians, sin, cos, 
 						tan, hypot)
'
* When you import something ,you can change the name of the imported item

'import math as math_ops'
In this case , you are importing the math module under the name math_ops. The math module will be added to your global namespace using the naming math_ops, and you can access the math module's content using the math_ops name
'print(math_ops.pi)'
#Why you want to use the import ... as statement to change the name of something when you import it
*1, to make a long or unwieldy name easier to type
*2, to avoid naming conflicts. For example, if you are using two packages that both define a module named utils, you might want to use the import ... as statement that the names are different, for example 
'from package1 import utils as utils1
from packages2 import utils as utils2
'
< Don't overuse it because every time you change the name of something ,you will have to remember that x is another name for Y, which adds complexity and means you have more things to remember as you write  your program
from ....import statement with import ....as 


Wildcare import 
import everything from a module or package in one fell swoop:
'from math import *'
relative imports 
from . import - the .character still refers to the current location and editor is the name of the package relative to this current location
Imagine that the slider module wants to import a module from the widgets directory:
from .. import controls

Why you use relative import statement 
1, there are a great way of making your import statements shorter and easier to read
2,When you write a package for others to use , you can have different modules within your package refer to each other without having to worry about where the user installed the package. For example , I might take a package you have written and place it inside another package; using relative imports, your package will continue to work without having to change all the import statements to reflect the new package structure
relative imports tend to violate the explicit is better than implicit principle. 


