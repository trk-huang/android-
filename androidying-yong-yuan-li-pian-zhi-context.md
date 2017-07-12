**Android Context的理解：**

Context，中文直译为“上下文”，官方文档中 的描述是：

Interface to global information about an application environment. This is an abstract class whose implementation

is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls

for application-level operations such as launching activities, broadcasting and receiving intents, etc

从上可知：

* Context描述的是一个应用程序环境的信息
* 该类是一个抽象类，Android提供了该抽象类的具体实现类Contextlml。
* 通过Context，可以获取应用程序的资源和类，也包括一些应用级别的操作，例如：启动一个activity、发送广播、接收intent的信息等。

一、Context相关类的继承关系：

![](http://hi.csdn.net/attachment/201203/1/0_1330607569Vj4c.gif)

**二、什么时候创建Context实例**

熟悉了Context的继承关系后，分析应用程序在什么情况下需要创建Context对象的？如下：

1. 创建Application对象时
2. 创建Service对象时
3. 创建Activity对象时

因此整个App引用公有的Context数目公式为：

总Context对象数 = Service个数 + Activity对象数 + 1\(application对应的Context实例\)

