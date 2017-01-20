# FoldLeft(左折叠)
> 我们想要一个操作任意列表的泛化函数……于是我们要泛化foldLeft这个函数

```Scala
scala> object FoldLeftList {
         def foldLeft[A, B](xs: List[A], b: B, f: (B, A) => B) = xs.foldLeft(b)(f)
       }
defined module FoldLeftList

scala> def sum[A: Monoid](xs: List[A]): A = {
         val m = implicitly[Monoid[A]]
         FoldLeftList.foldLeft(xs, m.mzero, m.mappend)
       }
sum: [A](xs: List[A])(implicit evidence$1: Monoid[A])A

scala> sum(List(1, 2, 3, 4))
res15: Int = 10

scala> sum(List("a", "b", "c"))
res16: String = abc

scala> sum(List(1, 2, 3, 4))(multiMonoid)
res17: Int = 24
```
> 现在我们可以用同样的方式抽象出FoldLeft这个类型类(typeclass)。

```Scala
scala> :paste
// 进入粘贴模式 (按ctrl-结束)

trait FoldLeft[F[_]] {
  def foldLeft[A, B](xs: F[A], b: B, f: (B, A) => B): B
}
object FoldLeft {
  implicit val FoldLeftList: FoldLeft[List] = new FoldLeft[List] {
    def foldLeft[A, B](xs: List[A], b: B, f: (B, A) => B) = xs.foldLeft(b)(f)
  }
}

def sum[M[_]: FoldLeft, A: Monoid](xs: M[A]): A = {
  val m = implicitly[Monoid[A]]
  val fl = implicitly[FoldLeft[M]]
  fl.foldLeft(xs, m.mzero, m.mappend)
}

// 退出粘贴模式，进入解释模式

warning: there were 2 feature warnings; re-run with -feature for details
defined trait FoldLeft
defined module FoldLeft
sum: [M[_], A](xs: M[A])(implicit evidence$1: FoldLeft[M], implicit evidence$2: Monoid[A])A

scala> sum(List(1, 2, 3, 4))
res20: Int = 10

scala> sum(List("a", "b", "c"))
res21: String = abc
```
这样Int和List类型上定义的函数都从sum函数上抽掉了。
## Scalaz中的类型类(Typeclass)
