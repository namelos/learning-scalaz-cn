## Equal
Haskell趣学指南写道：
> `Eq`用于支持相等判定(equality testing)的类型。这个函数的成员都实现`==`和`/=`。

在Scalaz中和`Eq`这个类型类等价的东西叫做`Equal`：
```Scala
scala> 1 === 1
res0: Boolean = true

scala> 1 === "foo"
<console>:14: error: could not find implicit value for parameter F0: scalaz.Equal[Object]
              1 === "foo"
              ^

scala> 1 == "foo"
<console>:14: warning: comparing values of types Int and String using `==' will always yield false
              1 == "foo"
                ^
res2: Boolean = false

scala> 1.some =/= 2.some
res3: Boolean = true

scala> 1 assert_=== 2
java.lang.RuntimeException: 1 ≠ 2
```

与标准库中的`==`不同，Equal通过声明`equal`方法来允许使用`===`，`=/=`，和`assert_===`操作符。主要区别就是当你尝试用`===`来判断`Int`和`String`之间是否相等时会产生编译时错误。  
注：我本来写的是`/==`而不是`=/=`，但Eiríkr Åsheim指出：
> 我看了你的教程。你应该鼓励人们用`=/=`而不是`/==`因为后者优先级很乱。

平常像`!=`这种比较运算符比`&&`和字母构成的方法等等的优先级要低一点。因为特殊优先级规则的原因`/==`因为不以`=`开头而以`=`结尾被当做赋值运算符，然后优先级降至最低(译注：在`Int` `2`中找不到`&&`方法，因为赋值优先级最低变成`2 && false`)：
```Scala
scala> 1 != 2 && false
res4: Boolean = false

scala> 1 /== 2 && false
<console>:14: error: value && is not a member of Int
              1 /== 2 && false
                      ^

scala> 1 =/= 2 && false
res6: Boolean = false
```
