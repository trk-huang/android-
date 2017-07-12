# 一、Intent概述

[Android](http://lib.csdn.net/base/android)中提供了Intent机制来协助各应用间的交互与通信。Intent负责对应用中一次操作的动作、动作涉及到的数据、附加数据等进行描述，[android](http://lib.csdn.net/base/android)则根据此Intent的描述，负责找到对应的组件，将相应数据传递给调用的组件并完成组件的调用。Intent不仅可用于应用程序之间，也可用于应用程序内部的Activity/Service之间的交互。因此，Intent在这里起着一个中介的作用，它专门提供组件相互调用的相关信息。

```
Intent的另一个用途是发送广播消息。应用程序和Android系统都可以使用Intent发送广播消息，广播消息的内容可以是与应用程序密切相关的数据信息，也可以是Android的系统信息，例如网络连接变化、电池电量变化、接收到短信和系统设置变化等。如果应用程序注册了BroadcastReceiver，则可以接收到指定的广播信息。

可见，作为不同UI间通信的信使，Intent相当于各个Activity间的桥梁。Activity之间通过Intent进行交互，可以通过Intent启动另外的Activity、启动Service、发起广播Broadcast等，并可通过Bundle传递数据。Intent的使用方式有以下3种：

方式一：通过startActivity\(\)来启动一个新的Activity，一般需要调用Context.startActivity\(\)或Context.startActivityForResult\(\)来传递Intent。

方式二：通过Broadcast机制可以将一个Intent发送给任何对这个Intent感兴趣的BroadcastReceiver，此时一般通过context.sendBroadcast\(\)、contex.sendOrderedBroadcast\(\)或context.sendStickyBroadcast\(\)方法传递。当Broadcast Intent被广播后，所有Intent-filter过滤条件满足的组件都将被激活。

方式三：当需要启动或绑定一个Service组件时，会通过context.startService\(Intent\)和context.bindService\(Intent, ServiceConnection, int\)来和后台的Service交互。
```

# 二、Intent属性

1. Component Name
2. Category
3. Action
4. Data
5. Extras
6. Flags

Intent分为显式Intent和隐式Intent。



# 三、Intent的匹配规则

Intent和IntentFilter配套使用的，具体而言，IntentFilter是每个组件的属性标签，在AndroidManifest.xml中是Intent的子节点。

系统接收到intent请求后会与现有的intent-filter进行匹配，然后选择最合适的组件元素以响应。

> IntentFilter：为某个组件向系统注册一些特性\(一个组件可以注册多个intentFilter\)，以便intent找到对应的组件。

Intent匹配的流程步骤：

1. 组件注册
2. 发起方主动向系统提供intent

影响匹配规则的因素有3个：

* Category
* Action
  * 如果filter没有任何action，那么所有intent都无法通过。
  * 如果filter中的action不为空，那么intent中不带action的filter可以通过。
  * 如果filter中的action不为空，且intent中的action也不为空，那么后者必须是前者的子集才能通过。
* Data\(URI和数据类型都要同时匹配\)
  * data的检验是最复杂的，通常包含两部分：MIME Type， URI
  * MIMEtype的目的很简单，就是指明某段数据是什么格式类型，以保障程序能正确解析处理。它是由两部分组件，type，subtype举例如下：

         &gt;Application/javascript,Application/zip;Audio/mp4,Audio/mpeg;Video/mp4;text/html;image/jpeg等

  * URI \(Uniform resource identifier\) 是某个资源的全球唯一定位标志。通常的格式是：scheme://   hosts: port/ path 

    * scheme:// 包括了“http"等网络协议，还有content表示本地Content provider所提供的数据。

    * “host”：是主机的名称

    * port： 通信的端口，统称为Authority

    * path： 文件的路径

而Extras和Flags则只有在选中的组件运行后才能起作用。一个组件可以同时声明多个IntentFilter，只要它包含的任何一个IntentFilter通过匹配验证，该组件就会被选中。如下图：

![](/assets/Intent.png)





