# ruby如何实现Module
-[Marc Steven](https://medium.com/@MarcStevenCoder)
---


在Ruby中module和class毕竟类似，你能像创建类一样创建module-通过输入关键字module去定义一系列方法。然而module和class是类似的，但是它们通过三种方式来处理；

* 1， Ruby不允许你通过module直接创建对象，在实践中这意味着你不能在一个module上调new方法，因为new是属于累的方法，而不是module
* 2，Ruby不允许你去配置一个超类给module
* 3，除此之外，你可以引入一个module通过include关键字

但是module准确来说是什么呢？ruby又是如何来呈现它们的，
## Module是类
事实证明，Ruby内部将模块实现为类。当您创建一个模块时，Ruby会创建另一个RClass/rb_classext_struct结构对，就像创建一个新类一样。例如，假设我们像这样定义一个新模块。

`module Professor
end
`
在内部，Ruby将创建一个类，而不是一个module.

