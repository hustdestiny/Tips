# 写在学习Swift之前
学习的时候已经是swift3.01了，开始对Swift不是很感冒。因为OC已经很强大,丰富的三方开源框架，不错的运行时，熟悉的语法，大量的项目基础。那么为什么还要替换成Swift呢？首先是Apple爸爸不遗余力的推，其次是Swift确实是一门先进的语言，类型安全什么的还是棒棒哒，编码阶段基本上就已经解决了大部分的崩溃。高阶函数，泛型，运算符重载，面向协议编程等一大堆特性，实在无法拒绝。它和很多语言都很像，大部分程序员转过来问题都不是很大。废话不多说，go！

## 初识Swift
它是一门新的编程语言，如果你对OC或者C比较熟悉的话，上手应该蛮快的。
C和OC中的类型，Swift提供了自己的一套类型，如Int,Double,Float,Bool,String,Array,Set,Dictionary,Tuple
Swift提供了变量和常量，其中常量比C中的常量更强大更安全
Swift中使用了大量的可选类型，可选类型意味着，它有可能有值，或者有可能是nil，注意nil和OC中的是有很大的区别的
Swift是一个类型安全的语言，很多错误在开发阶段就可以解决，你绝不允许将一个Int赋值给一个String

与其他类型一样变量和常量都有一个名字，变量通过var标识，常量通过let来标识，你可以在一行定义多个变量或者常量，中间用逗号分隔

可以显式的申明一个变量的类型``` var welcomeMessage: String ```,这意味着这个变量叫做 welcomeMessage 它的类型是String
在你不得不使用和系统关键字一样的名字的时候 使用`default`来标识
注释的写法也是和OC类似的
分号不必在每行代码的最后添加，但是如果在一行写了多个语句的话就必须使用分号将其分割

Int 在大部分情况下，你都应该使用这个默认的类型，在32位的平台下Int和Int32是一致的，但是在64的平台下它就和Int64是一致的。
Double和Float 其中Double代表的是64位的浮点数，而float则代表32位
Bool是true和false 这区别于 OC的Yes和No
Optional是Swift最大的特点，也是其他重要特性的基础，可以使用强制解包和可选值绑定的方式

Swift还拥有try catch的写法
assert也是支持的,但是他和OC的assert还是有区别的，主要是OC中的NSAssert只在DEBUG模式中起作用

## Swift中的基本的运算符
Swift中包含C中的绝大部分的运算符，但是改进了部分的运算符，比如说“=”这个运算符在Swift下就不再返回值了，这样预防了程序员在进行比较的时候 错误的将“==”写成了 “=”
Swift中增加了两个range的运算符 ```a..<b a...b```
Swift中包含单目，双目，和三目运算符
= 赋值运算符不会再返回值了
+-*/ 基本的数学运算符
% 取余运算符
== != > >= < <= 比较运算符
a??b 是Swift中新增加的运算符，可以把它理解成 a != nil ? a! : b
！ && || 逻辑运算符

## 终于轮到字符串啦
首先是为什么String不能用下标去遍历，我摔啊，我之前OC一直这么干哦！但是Apple爸爸说，不同的字符需要不同的存储量，为了确定字符的准确位置，必须从StartIndex和EndIndex开始遍历。话说回头，这个Index还真是难用。。
String是值类型，在赋值的时候都会自动copy一份

## 集合类型
Array,Dictionary,Set都是值类型
这个感觉什么好说哒

## 控制流
while,forin,if,guard,switch,break,continue,where

首先是while 和repeat-while，这个类似于C的while和do-while，讲道理的话是没啥好说的
然后是forin，这个在OC中也是较为常见，但是为什么不提供原始的for呢，摔啊
if的话有几点需要说，不需要加括号，得是bool值或者if let的语句，强大的if let 解包利器
switch就比较强大了，首先它不需要写break了，最重要的是，switch不仅仅是数字了，tuple和它的配合使用也是棒棒哒
guard 和if是类似的，它便于让程序尽早的退出，避免写出嵌套很深的代码，嗯嗯，👌

## 函数

## 闭包

## 枚举

## 类和结构

## 属性

## 方法

## 下标

## 继承

## 初始化

## 析构

## ARC

## Optional Chaining

## Error Handing

## 类型转换

## 

## 扩展

## 协议

## 泛型

## 访问限制

## 先进运算符
