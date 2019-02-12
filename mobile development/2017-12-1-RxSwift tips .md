# RxSwift tips 
##1 总是将你的系统或者其他部分模型化为纯函数。这些春函数能容易被测试，能常常用于修改操作行为
##2 当你使用Rx的时候，首先使用组成内建operator
##3 如果常常使用一些集合的运算，创建你自己的便利运算器

```
extension ObservableType where E: MaybeCool {

    @warn_unused_result(message="http://git.io/rxs.uo")
    public func coolElements()
        -> Observable<E> {
          return filter { e -> Bool in
              return e.isCool
          }
    }
}
```
## Rx操作尽可能一般，但是总是有edge case将难以模型化，在这些使用场景中，你能除垢剂你自己的运算器并且使用内建的operator中的一个作为引用。
## 总是使用operator去组合订阅
## 消除不惜一切代价的内嵌订阅，代码如下所示

```
textField.rx.text.subscribe(onNext: { text in
    performURLRequest(text).subscribe(onNext: { result in
        ...
    })
    .addDisposableTo(disposeBag)
})
.addDisposableTo(disposeBag)
```

使用操作符来完成链式的一次调用

```
textField.rx.text
    .flatMapLatest { text in
        // Assuming this doesn't fail and returns result on main scheduler,
        // otherwise `catchError` and `observeOn(MainScheduler.instance)` can be used to
        // correct this.
        return performURLRequest(text)
    }
    ...
    .addDisposableTo(disposeBag) // only one top most disposable

```

