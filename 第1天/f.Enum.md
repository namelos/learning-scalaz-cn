# Enum
Haskell趣学指南写道：
> Enum members are sequentially ordered types — they can be enumerated. The main advantage of the Enum typeclass is that we can use its types in list ranges. They also have defined successors and predecesors, which you can get with the succ and pred functions.

Scalaz与`Enum`类型类等价的是`Enum`:
```Scala
scala> 'a' to 'e'
res30: scala.collection.immutable.NumericRange.Inclusive[Char] = NumericRange(a, b, c, d, e)

scala> 'a' |-> 'e'
res31: List[Char] = List(a, b, c, d, e)

scala> 3 |=> 5
res32: scalaz.EphemeralStream[Int] = scalaz.EphemeralStreamFunctions$$anon$4@6a61c7b6

scala> 'B'.succ
res33: Char = C
```
不同于标准库中的`to`，`Enum`通过声明`Order`类型类中的`pred`和`succ`提供了返回`List`的`|->`函数。此外还有许多诸如`-+-`，`---`，`from`，`fromStep`，`pred`，`predx`，`succ`，`succx`，`|-->`，`|->`，`|==>`和`|=>`。这些运算符主要在枚举值中向前向后取值，然后返回值域(ranges)。
