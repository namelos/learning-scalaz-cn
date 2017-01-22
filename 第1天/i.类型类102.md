## 类型类102
I am now going to skip over to Chapter 8 Making Our Own Types and Typeclasses (Chapter 7 if you have the book) since the chapters in between are mostly about Haskell syntax.
## 红绿灯类型类
```Haskell
data TrafficLight = Red | Yellow | Green
```
在Scala里就是这样的：
```Scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

sealed trait TrafficLight
case object Red extends TrafficLight
case object Yellow extends TrafficLight
case object Green extends TrafficLight
```

现在我们来定义一个`Equal`的实例。
```Scala
scala> implicit val TrafficLightEqual: Equal[TrafficLight] = Equal.equal(_ == _)
TrafficLightEqual: scalaz.Equal[TrafficLight] = scalaz.Equal$$anon$7@2457733b
```
看看我们能用它吗？
```Scala
scala> Red === Yellow
<console>:18: error: could not find implicit value for parameter F0: scalaz.Equal[Product with Serializable with TrafficLight]
              Red === Yellow
```
所以显然`Equal[TrafficLight]`不好使，因为`Equal`的子类没变，都是`Equal[F]`(待定)。如果我把`TrafficLight`变成一个case class的话，那么`Red`和`Yellow`就会有同样的类型，但这样我就失去了sealed带来的严格模式匹配能力。#失败
```Scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

case class TrafficLight(name: String)
val red = TrafficLight("red")
val yellow = TrafficLight("yellow")
val green = TrafficLight("green")
implicit val TrafficLightEqual: Equal[TrafficLight] = Equal.equal(_ == _)
red === yellow

// Exiting paste mode, now interpreting.

defined class TrafficLight
red: TrafficLight = TrafficLight(red)
yellow: TrafficLight = TrafficLight(yellow)
green: TrafficLight = TrafficLight(green)
TrafficLightEqual: scalaz.Equal[TrafficLight] = scalaz.Equal$$anon$7@42988fee
res3: Boolean = false
```
Yes
