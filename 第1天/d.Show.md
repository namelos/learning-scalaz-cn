# Show
Haskell趣学指南写道：
> `Show`成员都可以以字符串形式展现。

Scalaz和`Show`类型类等价的就是`Show`：
```Scala
scala> 3.show
res14: scalaz.Cord = 3

scala> 3.shows
res15: String = 3

scala> "hello".println
"hello"
```
`Cord`显然是一个为长字符串准备的纯函数数据结构。
