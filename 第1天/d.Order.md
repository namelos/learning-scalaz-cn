## Order
Haskell趣学指南写道：
> `Ord`是为可排序的类型准备的。`Ord`包含如`>` ，`<`，`>=`，`<=`等所有标准比较函数。

Scalaz中和`Ord`对等的类型类是`Order`：
```Scala
scala> 1 > 2.0
res8: Boolean = false

scala> 1 gt 2.0
<console>:14: error: could not find implicit value for parameter F0: scalaz.Order[Any]
              1 gt 2.0
              ^

scala> 1.0 ?|? 2.0
res10: scalaz.Ordering = LT

scala> 1.0 max 2.0
res11: Double = 2.0
```

`Order`提供一个叫做`?|?`方法来返回`Ordering`类型：`LT`，`GT`，`EQ`。它也通过声明`order`方法提供了`lt`，`gt`，`lte`，`gte`，`min`，和`max`操作符。与`Equal`类似，如果将`Int`和`Double`进行比较的话也会导致编译时错误。
