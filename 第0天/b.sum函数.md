## sum函数
Nick用一个反复让sum函数更通用(general)的过程作为例子来讲解即时多态，首先是一个对Int类型列表(List)求和的函数：
```Scala
scala> def sum(xs: List[Int]): Int = xs.foldLeft(0) { _ + _ }
sum: (xs: List[Int])Int

scala> sum(List(1, 2, 3, 4))
res3: Int = 10
```

## Monoid(幺半群)
> 如果我们想进一步泛化(generalize)的话。我要先展示一个叫做Monoid的概念……这种类型实现一个叫做mappend的函数，它返回与它类型值域同样的类型；此外还有一个函数来返回一个零(zero)。

```Scala
scala> object IntMonoid {
         def mappend(a: Int, b: Int): Int = a + b
         def mzero: Int = 0
       }
defined module IntMonoid
```
如果我们把这个Monoid放进来的话，我们的函数看起来就泛化一些了。
```Scala
scala> def sum(xs: List[Int]): Int = xs.foldLeft(IntMonoid.mzero)(IntMonoid.mappend)
sum: (xs: List[Int])Int

scala> sum(List(1, 2, 3, 4))
res5: Int = 10
```
现在我们要抽象出Monoid这个类型了，于是我们就可以定义一个对所有A都成立的的Monoid。所以IntMonoid就是一个在Int范畴上的monoid：
```Scala
scala> trait Monoid[A] {
         def mappend(a1: A, a2: A): A
         def mzero: A
       }
defined trait Monoid

scala> object IntMonoid extends Monoid[Int] {
         def mappend(a: Int, b: Int): Int = a + b
         def mzero: Int = 0
       }
defined module IntMonoid
```

我们可以将sum定义成接受一个Int列表，和在Int范畴上的monoid作为参数的函数来求和：
```Scala
scala> def sum(xs: List[Int], m: Monoid[Int]): Int = xs.foldLeft(m.mzero)(m.mappend)
sum: (xs: List[Int], m: Monoid[Int])Int

scala> sum(List(1, 2, 3, 4), IntMonoid)
res7: Int = 10
```
> 我们不再使用Int类型定义的任何函数(译注：意思是不用Int本身的方法，如果你更熟悉OOP的话)，于是我们可以将所有的Int都替换成泛型(general type)：

```Scala
scala> def sum[A](xs: List[A], m: Monoid[A]): A = xs.foldLeft(m.mzero)(m.mappend)
sum: [A](xs: List[A], m: Monoid[A])A

scala> sum(List(1, 2, 3, 4), IntMonoid)
res8: Int = 10
```

> 最后一步，就是把这个Monoid变成隐式值，于是我们就不用每次都把它当参数传进来了。

```Scala
scala> def sum[A](xs: List[A])(implicit m: Monoid[A]): A = xs.foldLeft(m.mzero)(m.mappend)
sum: [A](xs: List[A])(implicit m: Monoid[A])A

scala> implicit val intMonoid = IntMonoid
intMonoid: IntMonoid.type = IntMonoid$@3387dfac

scala> sum(List(1, 2, 3, 4))
res9: Int = 10
```
虽然Nick没这么做，但是隐式参数经常以context bound(上下文绑定)的方式来声明：

```Scala
scala> def sum[A: Monoid](xs: List[A]): A = {
         val m = implicitly[Monoid[A]]
         xs.foldLeft(m.mzero)(m.mappend)
       }
sum: [A](xs: List[A])(implicit evidence$1: Monoid[A])A

scala> sum(List(1, 2, 3, 4))
res10: Int = 10
```
> 现在我们的sum函数已经非常泛化了，它可以将任何一个求和任何一个monoid列表。我们可以写个String的Monoid来实验下。我也要把这些都放到一个叫做Monoid的对象(object)中。这样做的原因是Scala的隐式获取规则(implicit resolution rules)：当它需要某个类型的隐式参数时，它就会在作用域中搜索所有的东西。它会将你需要的伴生对象(companion object)导入进来。

```Scala
scala> :paste
// 进入粘贴模式 (按ctrl-D可以退出)

trait Monoid[A] {
  def mappend(a1: A, a2: A): A
  def mzero: A
}
object Monoid {
  implicit val IntMonoid: Monoid[Int] = new Monoid[Int] {
    def mappend(a: Int, b: Int): Int = a + b
    def mzero: Int = 0
  }
  implicit val StringMonoid: Monoid[String] = new Monoid[String] {
    def mappend(a: String, b: String): String = a + b
    def mzero: String = ""
  }
}
def sum[A: Monoid](xs: List[A]): A = {
  val m = implicitly[Monoid[A]]
  xs.foldLeft(m.mzero)(m.mappend)
}

// 退出粘贴模式，进入解释模式.

defined trait Monoid
defined module Monoid
sum: [A](xs: List[A])(implicit evidence$1: Monoid[A])A

scala> sum(List("a", "b", "c"))
res12: String = abc
```
> 你还是可以为不同的monoid直接提供函数定义。我们可以直接给Int提供一个乘法意义上的monoid实例。
(译注：monoid中的mzero被称为单位元，比如加法的单位元是0，上面字符串拼接的单位元是""，而这里乘法的单位元是1。一个类型的值mappend了mzero还是这个值本身。)

```Scala
scala> val multiMonoid: Monoid[Int] = new Monoid[Int] {
         def mappend(a: Int, b: Int): Int = a * b
         def mzero: Int = 1
       }
multiMonoid: Monoid[Int] = $anon$1@48655fb6

scala> sum(List(1, 2, 3, 4))(multiMonoid)
res14: Int = 24
```
