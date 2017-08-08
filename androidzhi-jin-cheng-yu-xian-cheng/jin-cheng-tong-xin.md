# 进程通信

### 前言

在Android的世界里，App是有一个或多个的进程。在Java里，每一个虚拟机是一个进程，Android也是虚拟机机制，每启动一个App，默认就会启动一个虚拟机。在App里，有还被运行创建其他的进程，在主进程结束后，这个进程还可以独立运行。

#### 进程间通信介绍

进程间通信（Inter-process Communication），简称 IPC。就是指进程与进程间的通信，当我们的app有多个进程的时候，或者需要远程服务调用时，会进行进程间通信。

#### 设置多进程

Android设置多进程的步骤很简单，只要在manifest.xml文件中添加process属性：

```java
<service
    android:name=".MessagerService"
    android:process=":messager">
</service>
```

> 最终新的进程名称会是：包名+':messager'

虽然多进程设置简单，但是设置多进程之后，会带来一系列的问题：

1. 两个进程对应的是不同的内存区域
2. Application会创建多次
3. 静态成员不共用
4. 同步锁失效
5. 单例模式失效
6. 数据传递的对象必须序列化

#### 通信

跨进程通信的方式有多种:

1. Intent
2. Messenger通信
3. AIDL

> Intent

Intent进行的数据传递是我们平时最常见到的，它的原理就是对Binder进行了封装，但是它只能进行单向的数据传递，所以不能进行良好的进程间通信。

> Messenger 通信

Messenger的底层也是由Binder进行构建的，它是基于AIDL的基础上进行了封装。一般来说，Android是通过Binder来绑定服务，服务端主要是运行Service，客户端通过bindService获取到相关的Binder，Binder就作为桥梁进行跨进程通信。

下面我们来做一个演示：应用内的多进程通信，使用Messenger进行通信

##### 服务端

首选我们先创建一个Service：

```java
/**
 * Created by huangdaju on 17/8/8.
 */

public class ServerService extends Service {

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

}
```

在AndroidManifest.xml中设置进程

在Service中创建一个Handler，用来接收消息

创建一个Messenger，并把Handler传入进去

重写onBinder方法，返回Messenger里面的Binder：



##### 客户端

1.创建一个ServiceConnection：

2.在Activity中，绑定服务端的Serivce

3.在ServiceConnection中创建一个Handler用来接收消息

4.创建一个Messenger，并把Handler传入进去

5.

弊端

上面我们已经实现了应用内跨进程操作，但是里面其实是有缺陷的，服务端处理客户端的消息是串行的，消息是一条条处理，所以如果并发量大的话，通过Messager来通信就不大合适了。

> AIDL
>
> Note: Using AIDL is necessary only if you allow clients from different applications to access your service for IPC and want to handle multithreading in your service. If you do not need to perform concurrent IPC across different applications, you should create your interface by implementing a Binder or, if you want to perform IPC, but do not need to handle multithreading, implement your interface using a Messenger. Regardless, be sure that you understand Bound Services before implementing an AIDL.

主要意思就是你可以用 Messenger 处理简单的跨进程通信,但是高并发量的要用AIDL

AIDL支持的数据类型：

1. 基本数据类型
2. 实现了ParceLabel接口的对象
3. ArrayList，并且里面的元素也是要支持AIDL的
4. HashMap，并且里面的元素也是要支持AIDL的

这里有三个需要注意的地方：

1. 这里声明方法时,需要在参数前面增加一个tag,这个tag有三种,in,out,inout,这里表示的是这个参数可以支持的流向:

   1. **in:**这个对象能够从客户端到服务器,但是作为返回值从服务器到客户端的话数据不会传送过去\(不会为null,但是字段都没有赋值\)

   2. **out:**这个对象能够作为返回值从服务器到客户端,但是从客户端到服务器数据会为空\(不会为null,但是字段都没有赋值\)

   3. **inout:**能从客户端到服务器,也可以作为返回值从服务器到客户端

最后，我们还需要注意的地方：

* startService和stopService需要用同一个Intent对象

* bindService和unbindService需要用同一个ServiceConnection对象

* 5.0以后隐式意图开启或者绑定service要setPackage\(包名\)



