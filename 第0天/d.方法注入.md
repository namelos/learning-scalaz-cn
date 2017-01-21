## 方法注入(Method injection)(用来扩展我们的函数库)
> 如果我们要写一个用Monoid求和的函数，我们得这样调用：

```Scala
scala> def plus[A: Monoid](a: A, b: A): A = implicitly[Monoid[A]].mappend(a, b)
plus: [A](a: A, b: A)(implicit evidence$1: Monoid[A])A

scala> plus(3, 4)
res25: Int = 7
```
我们想提供一个操作符。但是我们不想只给一个类型提供，而是给所有的Monoid所有的实例提供这个操作符。我们用Scalaz 7的风格来实现它。

```Scala
scala> trait MonoidOp[A] {
         val F: Monoid[A]
         val value: A
         def |+|(a2: A) = F.mappend(value, a2)
       }
defined trait MonoidOp

scala> implicit def toMonoidOp[A: Monoid](a: A): MonoidOp[A] = new MonoidOp[A] {
         val F = implicitly[Monoid[A]]
         val value = a
       }
toMonoidOp: [A](a: A)(implicit evidence$1: Monoid[A])MonoidOp[A]

scala> 3 |+| 4
res26: Int = 7

scala> "a" |+| "b"
res28: String = ab
```
我们只用一个定义就同时为Int和String都注入了|+|方法。
## 标准类型语法
借助同样的方法，Scalaz也为标准库的类型如Option和Boolean提供了方法注入。

```Scalaz
scala> 1.some | 2
res0: Int = 1

scala> Some(1).getOrElse(2)
res1: Int = 1

scala> (1 > 10)? 1 | 2
res3: Int = 2

scala> if (1 > 10) 1 else 2
res4: Int = 2
```
希望你可以对Scalaz的找到一些感觉。
