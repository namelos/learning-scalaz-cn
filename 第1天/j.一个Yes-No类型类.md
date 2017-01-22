## 一个Yes-No类型类
我们看看我们能不能用Scalaz的风格自己实现我们的真值类型类。但我想换成自己的命名约定。比如Scalaz经常会分别调用三四个叫做`Show`，`show`和`show`的类型类，真是让人摸不到头脑。
类似`CanBuildFrom`，我想将给类型类加上`Can`前缀，然后像sjson/sbinary一样将方法命名成动词加上`s`的形式。因为`yesno`听起来没啥意义，我们还是称之为`truthy`比较好。最终我们的目标是让`1.truthy`返回`true`。不好的一点就是如果我们想将类型类实例当做函数使用的时候，比如`CanTruthy[Int].truthys(1)`的时候就要接上个s。
```Scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

trait CanTruthy[A] { self =>
  /** @return true, if `a` is truthy. */
  def truthys(a: A): Boolean
}
object CanTruthy {
  def apply[A](implicit ev: CanTruthy[A]): CanTruthy[A] = ev
  def truthys[A](f: A => Boolean): CanTruthy[A] = new CanTruthy[A] {
    def truthys(a: A): Boolean = f(a)
  }
}
trait CanTruthyOps[A] {
  def self: A
  implicit def F: CanTruthy[A]
  final def truthy: Boolean = F.truthys(self)
}
object ToCanIsTruthyOps {
  implicit def toCanIsTruthyOps[A](v: A)(implicit ev: CanTruthy[A]) =
    new CanTruthyOps[A] {
      def self = v
      implicit def F: CanTruthy[A] = ev
    }
}

// Exiting paste mode, now interpreting.

defined trait CanTruthy
defined module CanTruthy
defined trait CanTruthyOps
defined module ToCanIsTruthyOps

scala> import ToCanIsTruthyOps._
import ToCanIsTruthyOps._
```
