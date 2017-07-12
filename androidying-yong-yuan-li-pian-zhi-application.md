**Android Application概念**

Android中提供了Application这样一个类；看一下Android官方文档对此类的解释：

Base class for those who need to maintain global application state.

You can provide your own implementation by specifying its name in your AndroidManifest.xml's &lt;application&gt; tag,

which will cause that class to be instantiated for you when the process for your application/package is created.

大概意思就是：需要为应用程序提供全局变量，在AndroidManifest.xml中指定所实现的Application子类；

当你的Application进程被建立时，此类被实例化；

文档解释中也提到，实现Application子类并不是必须的；

在实现HelloWord程序里面，就没有实现Application子类，但是系统会为我们默认一个；

就是程序运行还是有Application概念的但不是核心，一个Application是一个进程，Application为整个程序提供Context； 此类使用非常简单。

## Application的作用:

1.定义全局属性和全局方法。

2.在应用程序组件中传递对象。

3.定义缓存。

## Application的生命周期:

Application 的生命周期是整个程序最长的，它的生命周期相当于程序的生命周期。

Application 为应用程序的创建终止，低可用内存和配置改变提供了时间处理程序，我们只需要重写以下只写方法

### onCreate方法 

在创建应用程序的时候调用。可以使用方法去初始化一些全局属性。

### onLowMemory方法

这个方法一般只会在后台进程已经终止，前台应用程序仍然缺少内存时调用。可以在这个方法内清空缓存或者释放不必要的资源。

### onTrimMemory方法

作为OnLowMemory的一个特定于应用程序的替代选择，在Android4.0\(API level 13\)中引入。当运行时绝顶当前应用程序应该尝试减少其内存开销时\(通常是它进入后台时\)调用。

### onConfigurationChanged方法

与activity不同，在配置改变时，应用程序对象不会被终止或重启。如果应用程序使用的值依赖于特定的配置，则重写这个方法来重新加载这些值或者在应用程序级别处理配置改变。

