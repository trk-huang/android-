# Android性能优化之性能调优



**一、性能瓶颈点**

整个页面主要由6个Page的ViewPager，每个Page为一个GridView，GridView一屏大概显示4\*4的item信息由于网络数据获取较多且随时需要保持页面内app下载进度及状态，所以出现以下性能问题

a.  ViewPager左右滑动明显卡顿

b.  GridView上下滚动明显卡顿

c.  其他Activity返回ViewPager Activity较慢

d.  网络获取到展现速度较慢

**二、性能调试及定位**

主要使用Traceview、monkey、monkey runner调试，traceview类似java web调优的visualvm，使用方法如下：

在需要调优的activity onCreate函数中添加

```java
android.os.debug.startMethodTracing("Entertainment");

```

onDestrory函数中添加:

```java
android.os.debug.stopMethodTracing();
```

程序退出后会在sd卡根目录下生成Entertainment.trace这个文件，cmd到android sdk的tools目录下运行traceview.bat Entertainment.trace即可。

可以看出各个函数的调用时间、调用次数、平均调用时间、时间占用百分比等从而定位到耗时的操作。



**三、性能调优点**

主要包括**同步改异步、缓存、Layout优化、数据库优化、算法优化、延迟执行。**

**1. 同步改异步**

这个就不用多讲了，耗时操作放在线程中执行防止占用主线程，一定程度上解决anr。

但需要注意线程和service结合（防止activity被回收后线程也被回收）以及线程的数量

线程池使用可见java的线程池



**2. 缓存**

java的对象创建需要分配资源较耗费时间，加上创建的对象越多会造成越频繁的gc影响系统响应。主要使用**单例模式、缓存\(图片缓存、线程池、View缓存、IO缓存、消息缓存、通知栏notification缓存\)及其他方式**减少对象创建。

**\(1\). 单例模式**

对于创建开销较大的类可使用此方法，保证全局一个实例，在程序运行过程中该类不会因新建额外对象产生开销。示例代码如下：

```java
public class Singleton {
 
    private static Object    obj      = new Object();
    private static Singleton instance = null;
 
    private Singleton(){
    }
 
    public static Singleton getInstance() {
        // if already inited, no need to get lock everytime
        if (instance == null) {
            synchronized (obj) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
 
        return instance;
    }
}
```

\(2\). 缓存

程序中用到了**图片缓存、线程池、View缓存、IO缓存、消息缓存、通知栏notification缓存**等。

**a. 图片缓存：**见ImageCacheImageSdCache和

**b. 线程池：**使用Java的Executors类，通过newCachedThreadPool、newFixedThreadPool、newSingleThreadExecutor、newScheduledThreadPool提供四种不同类型的线程池

**c. View缓存：**

可见ListView缓存机制

```java
@Override
public View getView(int position, View convertView, ViewGroup parent) {
	ViewHolder holder;
	if (convertView == null) {
		convertView = inflater.inflate(R.layout.type_item, null);
		holder = new ViewHolder();
		holder.imageView = (ImageView)convertView.findViewById(R.id.app_icon);
		holder.textView = (TextView)convertView.findViewById(R.id.app_name);
		convertView.setTag(holder);
	} else {
		holder = (ViewHolder)convertView.getTag();
	}
	holder.imageView.setImageResource(R.drawable.index_default_image);
	holder.textView.setText("");
	return convertView;
}
 
/**
 * ViewHolder
 */
static class ViewHolder {
 
	ImageView imageView;
	TextView  textView;
}
```

通过convertView是否为null减少layout inflate次数，通过静态的ViewHolder减少findViewById的次数，这两个函数尤其是inflate是相当费时间的



**d. IO缓存：**

使用具有缓存策略的输入流，BufferedInputStream替代InputStream，BufferedReader替代Reader，BufferedReader替代BufferedInputStream.对文件、网络IO皆适用。



**e. 消息缓存：**通过 Handler 的 obtainMessage 回收 Message 对象，减少 Message 对象的创建开销

handler.sendMessage\(handler.obtainMessage\(1\)\);



**f. 通知栏notification缓存：**下载中需要不断改变通知栏进度条状态，如果不断新建Notification会导致通知栏很卡。这里我们可以使用最简单的缓存

Map&lt;String, Notification&gt; notificationMap = new HashMap&lt;String, Notification&gt;\(\);如果notificationMap中不存在，则新建notification并且put into map.

**4. 数据库优化**

主要包括索引和事务及针对Sqlite的优化。

**3.Layout优化**

使用抽象布局标签\(include, viewstub, merge\)、去除不必要的嵌套和View节点、减少不必要的infalte及其他Layout方面可调优点，顺带提及布局调优相关工具\(hierarchy viewer和lint\)。具体可见性能优化之布局优化

TextView属性优化：TextView的android:ellipsize=”marquee”跑马灯效果极耗性能，具体原因还在深入源码中

**5. 算法优化**

这个就是个博大精深的话题了，只介绍本应用中使用的。

使用hashMap代替arrayList，时间复杂度降低一个数量级



**6. 延迟执行**

对于很多耗时逻辑没必要立即执行，这时候我们可以将其延迟执行。

**线程延迟执行** ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool\(10\);

**消息延迟发送**handler.sendMessageDelayed\(handler.obtainMessage\(0\), 1000\)

