# Bounded
> `Bounded`成员分别都有一个上界和一个下界(an upper and a lower bound)。

Scalaz和`Bounded`等价的也是`Enum`
```Scala
scala> implicitly[Enum[Char]].min
res43: Option[Char] = Some(?)

scala> implicitly[Enum[Char]].max
res44: Option[Char] = Some( )

scala> implicitly[Enum[Double]].max
res45: Option[Double] = Some(1.7976931348623157E308)

scala> implicitly[Enum[Int]].min
res46: Option[Int] = Some(-2147483648)

scala> implicitly[Enum[(Boolean, Int, Char)]].max
<console>:14: error: could not find implicit value for parameter e: scalaz.Enum[(Boolean, Int, Char)]
              implicitly[Enum[(Boolean, Int, Char)]].max
```
`Enum`类型类实例可以返回最大值`Option[T]`(译注：当然可能也没有最大值，因此是Option[T])。
