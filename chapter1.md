# 基础组件介绍

Android的组件包含四大基础组件：Activity、BroadcastReceiver、Service、Content Provider。

其他的可以包含比如Context、Application、Fragment、Intent、Loader、Window

### 一、Android四大基本组件介绍

**Activity：**

应用程序中，一个Activity通常就是一个单独的屏幕，它上面可以显示一些控件也可以监听并处理用户的事件，做出响应。

Activity之间通过Intent进行通信。在Intent的描述结构中，有两个重要部分：action和Bundle、IntentFilter

典型的动作类型有：MAIN、VIEW、PICK、EDIT等。而对应的数据则以URI的形式进行表示。

* 与之有关系的一个类叫ItentFilter。相对于intent是一个有效的做某事的请求，IntentFilter则用于描述一个activity能够操作哪些intent。IntentFilter通常是在Androidmanifest.xml中定义。activity的跳转是通过startActivity\(Intent intent\)方法。

* Activity能够重复利用其它组件中以intent的形式产生一个请求；

* Activities 可以在任何时候被一个具有相同IntentFilter的新的activity取代；

AndroidManifest文件中含有如下过滤器的Activity组件为默认启动类当程序启动时系统自动调用它

```java
<intent-filter>
       <action android:name="android.intent.action.MAIN" />
       <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

**BroadcastReceiver广播接收器：**

它可以对外部事件进行过滤只对感兴趣的外部事件进行接收并作出响应。广播接收器没有界面。但是，它可以启动一个activity或者一个service来响应它的消息。或者使用Notification来通知用户。通知可以用很多种方式来吸引用户注意力--闪动背灯、震动、播放声音。一般来说是在状态栏上放一个持久的图标，用户可以打开它并获取消息。

广播的类型：

普通广播：通过Context.sendBroadcast\(Intent intent\)发送的

有序广播：通过Context.sendOrderedBroadcast\(intent,receiverPermisssion\)发送的，该方法第2个参数决定该广播的级别，级别数值在-1000到1000之间，值越大，发送的优先级就越高；广播接收者接收广播时的级别可通过IntentFilter中priority进行设置，同级别接收的先后是随机的，再到级别低的收到广播，高级别的或同级别先收到广播的可以通过abortBroadcast\(\)方法截断广播使其他的接收者无法收到该广播。

监听广播intent步骤

* 写一个继承BroadCastReceiver的类，重写onReceive\(\)方法，广播接收器仅在它执行这个方法时处于活跃状态。当onReceive\(\)返回后，它就为失活状态。

注意：为了保证用户交互过程的流畅，一些耗时的操作要放到线程里。

* 注册广播有两种方式：动态注册、Androidmanifest.xml静态注册,可理解为系统中注册

```java
<receiver android:name=".SMSBroadcastReceiver" >
　　<intent-filter android:priority = "2147483647" >
　　　　<action android:name="android.provider.Telephony.SMS_RECEIVED" />
　　</intent-filter>
</receiver >
```

静态注册,注册的广播，下面的priority表示接收广播的级别"2147483647"为最高优先级

```java
IntentFilter intentFilter=new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
registerReceiver(mBatteryInfoReceiver ,intentFilter);

//反注册
unregisterReceiver(receiver);
```

动态注册，一般在Activity可交互时onResume\(\)内注册BroadcastReceiver

注意：

1. 生命周期只有+秒左右，如果在onReceive\(\)内做超过十秒的事情，就会报ANR错误。如果需要完成一项比较耗时的工作 , 应该通过发送 Intent 给 Service, 由Service 来完成 . 这里不能使用子线程来解决 , 因为 BroadcastReceiver 的生命周期很短 , 子线程可能还没有结束BroadcastReceiver 就先结束了 .BroadcastReceiver 一旦结束 , 此时 BroadcastReceiver 的所在进程很容易在系统需要内存时被优先杀死 , 因为它属于空进程 \( 没有任何活动组件的进程 \). 如果它的宿主进程被杀死 , 那么正在工作的子线程也会被杀死 . 所以采用子线程来解决是不可靠的
2. 动态注册广播接收器还有一个特点，就是当用来注册的Activity关掉后，广播也就失效了。静态注册无需担忧广播接收器是否被关闭,只要设备是开启状态,广播接收器也是打开着的。也就是说哪怕app本身未启动,该app订阅的广播在触发时也会对它起作用系统常见广播Intent,如开机启动、电池电量变化、时间改变等广播

**Service 服务：**

一个Service是一段长生命周期，没有用户界面的Android组件，可以用来开发如监控类的软件

比如一个正在播放列表中播放歌曲的媒体播放器。当导航到其他屏幕时，音乐还需要继续播放，然而又没有对应的activity存在，该怎么办？这时，activity会使用context.startService\(\)来启动一个service，从而保持音乐的播放。

Service的使用步骤

1. 继承Service类
2. 在Androidmanifest.xml中注入该Service，例如
   1. &lt;Service name=".SMSService" /&gt;
3. 通过Context.startservice\(\)或者Context.bindService\(\)来启动

startService和bindService，这两种Service的启动方式也是有差异的。

1. startService:该启动方式和调用者是没有关系的，即使调用者关闭了，service会依然存在，如果service想要被停止，可以用Context.stopService\(\)来结束，这时候Service会调onDestroy\(\)方法；这个方法首次启动Service时，系统会先调用onCreate（）-&gt;onStart\(\),以后每次启动只调用onStart\(\)。
2. bindService：启动的服务与调用者存在关系，可以进行数据交互，只要调用者结束了，这个服务就自然得结束了。使用此方法首次启动时，系统会先调用onCreate\(\)-&gt;onBind\(\)，以后每次调用启动时，系统只会调用onBind\(\),想主动解除绑定可使用Context.unbindService\(\),系统会依次调用onUnbind\(\)-&gt;onDestroy\(\)。

**Content Provider内容提供者 :**

android平台提供了Content Provider使一个应用程序的指定数据集提供给其他应用程序。这些数据可以存储在文件系统中、在一个SQLite数据库、或以任何其他合理的方式,其他应用可以通过ContentResolver类\(见ContentProviderAccessApp例子\)从该内容提供者中获取或存入数据.\(相当于在应用外包了一层壳\),只有需要在多个应用程序间共享数据是才需要内容提供者。例如，通讯录数据被多个应用程序使用，且必须存储在一个内容提供者中它的好处:统一数据访问方式。

**关于四大基本组件的一个总结：**

> 1. 四大组件的注册：Activity、Service、Content provider 内容提供者都需要在AndroidManifest.xml中声明：  
>    1. 注册格式：  
>       1. &lt;Activity&gt; 声明Activity  
>       2. &lt;Service&gt; 声明Service  
>       3. &lt;recevier&gt; 声明receiver  
>       4. &lt;provider&gt; 声明内容提供者
>
>    而BroadCastReceiver广播接收者的注册可以静态注册外，还可以通过动态代码注册创建,调用Context.registerReceiver\(\)的方式注册系统。
>
> 2. 四大组件的激活：
>
>    1. 容提供者的激活：当接收到ContentResolver 发出的请求后，内容提供者被激活。而其它三种组件──activity、服务和广播接收器被一种叫做intent 的异步消息所激活
>
>    2. Activity的激活：通过Context.startActivity\(\)或者Context.startActivityForResult\(\)的方式，加载一个activity，相应的activity可以通过getIntent\(\)来查看激活它的intent。如果想要返回结果，用Context.startActivityForREsult\(\);
>
>    3. 服务的激活：Context.startService\(\)或者Context.bindService\(\),前者Android 调用服务的onStart\(\)方法并将Intent 对象传递给它，后者Android 调用服务的onBind\(\)方法将这个Intent 对象传递给它
>
> 3. 四大组件的关闭：
>
>    内容提供者仅在响应ContentResolver 提出请求的时候激活。而一个广播接收器仅在响应广播信息的时候激活。所以，没有必要去显式的关闭这些组件。Activity关闭：可以通过调用它的finish\(\)方法来关闭一个activity服务关闭：对于通过startService\(\)方法启动的服务要调用Context.stopService\(\)方法关闭服务，使用bindService\(\)方法启动的服务要调用Contex.unbindService \(\)方法关闭服务
>
> 四大组件的生命周期:
>
> Android系统是一个多任务的multi-task的操作系统，可以执行多进程任务。每多开一个程序，就会消耗一些内存。当同时执行过多程序时，或者关闭的程序内存没有彻底释放，就影响了设备的运行，设备会越来越慢，甚至不稳定。
>
> 因此引入了生命周期的概念：（Life cycle）
>
> Android应用程序的生命周期是由android Framework来管理的，当一个程序运行时，系统就会创建一个进程，当系统内存不足的时候，就会按应用的优先级来结束。

Activity的生命周期：

> ![](http://pic002.cnblogs.com/images/2012/402670/2012050219053256.jpg)

Activity整个生命周期包含4种状态，3个循环，7个重要方法。

4种状态

1. 活动状态（active / running）
   1. 当前的activity处于屏幕的前端（任务栈的最上面），此时它获取了焦点并影响用户操作。同一时刻，只有一个activity处于活动状态。
2. 暂停状态（pause）
   1. 当activity失去焦点的时候，比如Toast或者dialog、透明的activity覆盖在上面的时候，这个activity处于暂停状态，暂停的acitivty仍然处于存活状态（保留了当前的activity的所有状态和成员变量，并与窗口保持连接），但是当系统内存吃紧的时候，该Activity可能被kill。
3. 停止状态 （stop）
   1. 完全被另一个Activity遮挡时处于停止状态，它仍然保留着所有的状态和成员信息。只是对用户不可见,当其他地方需要内存时它往往被系统杀掉
4. 非活动状态 （dead）
   1. Activity 尚未被启动、已经被手动终止，或已经被系统回收时处于非活动的状态，要手动终止Activity，可以在程序中调用"finish"方法。如果是（按根据内存不足时的回收规则）被系统回收，可能是因为内存不足了

7个重要方法：

当Activity第一次被实例化的时候系统会调用,

整个生命周期只调用1次这个方法

通常用于初始化设置: 1、为Activity设置所要使用的布局文件2、为按钮绑定监听器等静态的设置操作

      onCreate\(Bundle savedInstanceState\);



当Activity可见未获得用户焦点不能交互时系统会调用

      onStart\(\);



当Activity已经停止然后重新被启动时系统会调用

      onRestart\(\);



当Activity可见且获得用户焦点能交互时系统会调用

      onResume\(\);



当系统启动另外一个新的Activity时,在新Activity启动之前被系统调用保存现有的Activity中的持久数据、停止动画等,这个实现方法必须非常快。当系统而不是用户自己出于回收内存时，关闭了activity 之后。用户会期望当他再次回到这个activity 的时候，它仍保持着上次离开时的样子。此时用到了onSaveInstanceState\(\)，方法onSaveInstanceState\(\)用来保存Activity被杀之前的状态,在onPause\(\)之前被触发,当系统为了节省内存销毁了Activity\(用户本不想销毁\)时就需要重写这个方法了,当此Activity再次被实例化时会通过onCreate\(Bundle savedInstanceState\)将已经保存的临时状态数据传入因为onSaveInstanceState\(\)方法不总是被调用,触发条件为\(按下HOME键,按下电源按键关闭屏幕,横竖屏切换情况下\),你应该仅重写onSaveInstanceState\(\)来记录activity的临时状态，而不是持久的数据。应该使用onPause\(\)来存储持久数据。

      onPause\(\);



当Activity被新的Activity完全覆盖不可见时被系统调用

      onStop\(\);



当Activity\(用户调用finish\(\)或系统由于内存不足\)被系统销毁杀掉时系统调用,（整个生命周期只调用1次）用来释放onCreate \(\)方法中创建的资源,如结束线程等

      onDestroy\(\);

3个嵌套循环：

1.Activity完整的生命周期:从第一次调用onCreate\(\)开始直到调用onDestroy\(\)结束

2.Activity的可视生命周期:从调用onStart\(\)到相应的调用onStop\(\)

      在这两个方法之间,可以保持显示Activity所需要的资源。如在onStart\(\)中注册一个广播接收者监听影响你的UI的改变,在onStop\(\) 中注销。

3.Activity的前台生命周期:从调用onResume\(\)到相应的调用onPause\(\)。



**BroadcastReceiver广播接收器生命周期：**

生命周期只有十秒左右，如果在 onReceive\(\) 内做超过十秒内的事情，就会报ANR\(Application No Response\) 程序无响应的错误信息

它的生命周期为从回调onReceive\(\)方法开始到该方法返回结果后结束

**Service服务生命周期:**

  
![](http://pic002.cnblogs.com/images/2012/402670/2012050219083160.jpg)



Service完整的生命周期:从调用onCreate\(\)开始直到调用onDestroy\(\)结束

Service有两种使用方法：

1&gt;以调用Context.startService\(\)启动，而以调用Context.stopService\(\)结束

2&gt;以调用Context.bindService\(\)方法建立，以调用Context.unbindService\(\)关闭







