# 性能优化之Java\(Android\)代码优化

  
主要介绍Java代码中性能优化方式及网络优化，包括缓存、异步、延迟、数据存储、算法、JNI、逻辑等优化方式

**1、降低执行时间**

这部分包括：缓存、数据存储优化、算法优化、JNI、逻辑优化、需求优化几种优化方式。

**\(1\). 缓存**

缓存主要包括对象缓存、IO缓存、网络缓存、DB缓存，对象缓存能减少内存的分配，IO缓存减少磁盘的读写次数，网络缓存减少网络传输，DB缓存较少Database的访问次数。

在内存、文件、数据库、网络的读写速度中，内存都是最优的，且速度数量级差别，所以尽量将需要频繁访问或访问一次消耗较大的数据存储在缓存中。

Android中常使用缓存:

a.  线程池

b.  Android图片缓存Android图片Sdcard缓存，数据预取缓存，

c. 消息缓存

通过handler.obtainMessage复用之前的message

d.ListView缓存

e.网络缓存  
数据库缓存http response，根据http头信息中的Cache-Control域确定缓存过期时间。  
f. 文件IO缓存  
使用具有缓存策略的输入流，BufferedInputStream替代InputStream，BufferedReader替代Reader，BufferedReader替代BufferedInputStream.对文件、网络IO皆适用。  
g.layout缓存  
h. 其他需要频繁访问或访问一次消耗较大的数据缓存



**\(2\). 数据存储优化**

包括数据类型、数据结构的选择。

**a. 数据类型选择**

字符串拼接用StringBuilder代替String，在非并发情况下用StringBuilder代替StringBuffer。如果你对字符串的长度有大致了解，如100字符左右，可以直接new StringBuilder\(128\)指定初始大小，减少空间不够时的再次分配。

64位类型如long double的处理比32位如int慢

使用SoftReference、WeakReference相对正常的强应用来说更有利于系统垃圾回收

final类型存储在常量区中读取效率更高

LocalBroadcastManager代替普通BroadcastReceiver，效率和安全性都更高

**b. 数据结构选择**  
常见的数据结构选择如：  
ArrayList和LinkedList的选择，ArrayList根据index取值更快，LinkedList更占内存、随机插入删除更快速、扩容效率更高。一般推荐ArrayList。  
ArrayList、HashMap、LinkedHashMap、HashSet的选择，hash系列数据结构查询速度更优，ArrayList存储有序元素，HashMap为键值对数据结构，LinkedHashMap可以记住加入次序的hashMap，HashSet不允许重复元素。  
HashMap、WeakHashMap选择，WeakHashMap中元素可在适当时候被系统垃圾回收器自动回收，所以适合在内存紧张型中使用。  
Collections.synchronizedMap和ConcurrentHashMap的选择，ConcurrentHashMap为细分锁，锁粒度更小，并发性能更优。Collections.synchronizedMap为对象锁，自己添加函数进行锁控制更方便。



Android也提供了一些性能更优的数据类型，如SparseArray、SparseBooleanArray、SparseIntArray、Pair。  
Sparse系列的数据结构是为key为int情况的特殊处理，采用二分查找及简单的数组存储，加上不需要泛型转换的开销，相对Map来说性能更优。不过我不太明白为啥默认的容量大小是10，是做过数据统计吗，还是说现在的内存优化不需要考虑这些东西，写16会死吗，还是建议大家根据自己可能的容量设置初始值。



**\(3\). 算法优化**  
这个主题比较大，需要具体问题具体分析，尽量不用O\(n\*n\)时间复杂度以上的算法，必要时候可用空间换时间。  
查询考虑hash和二分，尽量不用递归。可以从结构之法 算法之道

**\(4\). JNI**

Android应用程序大都通过Java开发，需要Dalvik的JIT编译器将Java字节码转换成本地代码运行，而本地代码可以直接由设备管理器直接执行，节省了中间步骤，所以执行速度更快。不过需要注意从Java空间切换到本地空间需要开销，同时JIT编译器也能生成优化的本地代码，所以糟糕的本地代码不一定性能更优。  


**\(5\). 逻辑优化**

这个不同于算法，主要是理清程序逻辑，减少不必要的操作。



**\(6\). 需求优化**

这个就不说了，对于sb的需求可能带来的性能问题，只能说做为一个合格的程序员不能只是执行者，要学会说NO。不过不能拿这种接口敷衍产品经理哦。



**2、异步，利用多线程提高TPS**

充分利用多核Cpu优势，利用线程解决密集型计算、IO、网络等操作。

关于多线程可参考：Java线程池

在Android应用程序中由于系统ANR的限制，将可能造成主线程超时操作放入另外的工作线程中。在工作线程中可以通过handler和主线程交互。



**3、提前或延迟操作，错开时间段提高TPS**

**\(1\) 延迟操作**

不在Activity、Service、BroadcastReceiver的生命周期等对响应时间敏感函数中执行耗时操作，可适当delay。

Java中延迟操作可使用ScheduledExecutorService，不推荐使用Timer.schedule;

Android中除了支持ScheduledExecutorService之外，还有一些delay操作，如

handler.postDelayed，handler.postAtTime，handler.sendMessageDelayed，View.postDelayed，AlarmManager定时等。



**\(2\) 提前操作**

对于第一次调用较耗时操作，可统一放到初始化中，将耗时提前。如得到壁纸wallpaperManager.getDrawable\(\);

**4、网络优化**

以下是网络优化中一些客户端和服务器端需要尽量遵守的准则：  
a. 图片必须缓存，最好根据机型做图片做图片适配  
b. 所有http请求必须添加httptimeout

c. 开启gzip压缩  
d. api接口数据以json格式返回，而不是xml或html  
e. 根据http头信息中的Cache-Control及expires域确定是否缓存请求结果。

f. 确定网络请求的connection是否keep-alive  
g. 减少网络请求次数，服务器端适当做请求合并。  
h. 减少重定向次数  
i. api接口服务器端响应时间不超过100ms















