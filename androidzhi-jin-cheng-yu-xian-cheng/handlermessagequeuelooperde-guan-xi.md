# Handler、messageQueue、Looper的关系

简介：

相信不少人对这几个概念是深恶痛绝，因为它们“像风像雾又像雨”-------自我感觉很熟识，但是如果下一次相遇，却又陌生的很，因此，这次让我们彻底来解决这个问题。

先看下图：让我们来认识这些概念聚合在一起工作是怎么样子的。



![](/assets/未命名文件.png)

* Runnable和Message可以被亚茹某个MessageQueue中，形成一个集合。需要注意的是，一般MessageQueue只允许保存一种类型的Object。因此实际源码中，MessageQueue会先对Runnable进行相应的转换，然后放入MessageQueue中的。
* Looper循环地做某件事，如果队列为空，则进入休眠
* Handler是真正“处理事情“的地方，它利用自身的处理机制，对传入的各种Object进行相应的处理并产生结果。

> 一句话概括：Looper不断获取MessageQueue中的一个Message，然后由Handler去处理



