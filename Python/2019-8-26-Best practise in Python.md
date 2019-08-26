# Best practise in Python
## 1, Naming and using variable
* 1 Variable names can contain only **letters,numbers, and underscores**. They can start with a letter or underscore, **but not with a number**.For instance, you can call a variable message_1 but not 1_message.
* 2, **Space are not allowed in variable names ,but underscores can be used to separate words in variable names
`greeting_message //works

greeting message // will cause errors****`

* 3, avoid using Python keyword and function names as variable names;
* 4, Variable names should be short but descriptive. For example, **name** is better than **n**, **student_name** is better than s_n, **name_length** is better than **length_of_persons_name**.
* 4, Be careful when using the lowercase letter l and the uppercase letter O because they could be confused with the number 1 and 0.
* 5， Functions, variables , and attributes should be in lowercase_underscore format
* 6, Protected instance attributes should be in _leading_underscore format.
* 7, Private instance attributes should be in _double_leading_underscore format
* 8, Classes and exceptions should be in CapitalizedWord format
* 9, Module-level constants should be in ALL_CAPS format
* 10, Instance methods in class should use self as the name of the first parameter(which refers to the object)
* 11, Class methods should use cls as the name of the first parameter(which refers to the class)
## 2 Expressions and statements
* Use inline negation(if a is not b) instead of negation of positive expressions(if not a is b)
* Don't check for empty value(like[] or '') by checking the length(if len(somelist) ==0). Use if not somelist and assume empty values implicitly evaluate to False
* The same thing goes for non-empty value
* Avoid single-line if statements, for and while loops, and except compound statement. Spread these over multiple lines for clarity
* Always put import statements at the top of a file
* Always use absolute names for modules when importing them, not names relative to the current module's own path. For example, to import the foo module from the bar package, you should do from bar import foo, not just import foo.
* If you must do relative imports, use the explicit syntax from. import foo.
* Imports should be in sections in the following order:standard library modules, third party modules, your own module
## 3 The difference between bytes, str and unicode
In Python3, there are two types that represent sequences of characters:bytes and str. Instance of bytes contain raw 8-bit values, instance of str contain Unicode characters.
In Python2, there are two types that represent sequence of characters:str and unicode.the instance of str contain raw 8-bit values , Instances of unicode contain Unicode characters.
**There are many ways to represent Unicode characters as binary data(raw 8-bit values)**. The most common encoding is UTF-8. str instances in Python3 and unicode instances in Python2 don't have an associated binary encoding. To convert Unicode characters to binary data , you must use the **encode** method. To convert binary data to Unicode characters ,you must use the **decode** method.
 




