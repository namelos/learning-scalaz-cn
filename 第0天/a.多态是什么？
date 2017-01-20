# 多态是什么(Polymorphism)
## 参数化多态(Parametric polymorphism)
尼克说：
> 在这个head函数中，它取一个A类型的列表，返回一个A类型。并且它不关心A是什么：它可能是Ints， Strings，Oranges，Cars，啥都行。这对任意的A都成立，我们就说这个函数是定义可以接受任意的A。

```Scala
scala> def head[A](xs: List[A]): A = xs(0)
head: [A](xs: List[A])A

scala> head(1 :: 2 :: Nil)
res0: Int = 1

scala> case class Car(make: String)
defined class Car

scala> head(Car("Civic") :: Car("CR-V") :: Nil)
res1: Car = Car(Civic)
```

[Haskell wiki](http://www.haskell.org/haskellwiki/Polymorphism)写道：
> 参数化多态是指当一个值的类型包含一个或多个(未指定的)类型变量时，这个值就可以通过代入类型变量来变成具体类型。

## 子类多态(Subtype polymorphism)
我们思考下可以将两个A类型求和的`plus`函数：
```Scala
scala> def plus[A](a1: A, a2: A): A = ???
plus: [A](a1: A, a2: A)A
```
对不同的类型A，我们需要提供不同的求和方式。其中一个办法是通过子类化来实现这些。
```Scala
scala> trait Plus[A] {
         def plus(a2: A): A
       }
defined trait Plus

scala> def plus[A <: Plus[A]](a1: A, a2: A): A = a1.plus(a2)
plus: [A <: Plus[A]](a1: A, a2: A)A
```
现在我们起码能为不同的A类型提供不同的求和定义了。但是，这样我们定义数据类型时就要混入(实现)Plus这个trait(特质)。所以这样对Int和String这些内置类型就无能为力了。

## 即时多态(Ad-hoc polymorhism)
在Scala中，第三种实现多态的方式是为trait提供隐式转换(implicit conversion)或隐式参数(implicit parameters)。
```Scala
scala> trait Plus[A] {
         def plus(a1: A, a2: A): A
       }
defined trait Plus

scala> def plus[A: Plus](a1: A, a2: A): A = implicitly[Plus[A]].plus(a1, a2)
plus: [A](a1: A, a2: A)(implicit evidence$1: Plus[A])A
```
这是真正意义上的*即时*，因为：
1. 我们可以为不同类型的A提供不同的函数定义
2. 我们可以在不修改类型源码的情况下为类型(如Int这种内置类型)提供函数定义
3. 函数定义可以在不同作用域(scopes)下分别可用或不可用

最后一点使得Scala的即时多态比Haskell更强大。在这方面更多信息可以在[Debasish Ghosh @debasishg](https://twitter.com/debasishg)的[Scala Implicits : Type Classes Here I Come](http://debasishg.blogspot.com/2010/06/scala-implicits-type-classes-here-i.html)中找到。
接下来我们要分析下plus函数。sum
