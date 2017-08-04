# 贝赛尔曲线

贝塞尔曲线在图形图像学中又相当重要的地位，android的Path也提供了一些方法来给我们模拟低阶贝塞尔曲线。

贝塞尔曲线的原理就是一个起点，一个重点和至少0个控制点就可电仪一个贝塞尔曲线。

#### 当控制点为0时：一阶贝塞尔曲线

此时曲线就是一条线段，看下图：

![](http://upload-images.jianshu.io/upload_images/2086682-fca72ff778267929.gif?imageMogr2/auto-orient/strip)

公式：

![](http://upload-images.jianshu.io/upload_images/2086682-0135239707a03cf6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

b\(t\)就是运动点在t时刻的坐标，p0是起点，p1是终点



#### 二阶贝塞尔曲线

一个控制点

![](http://upload-images.jianshu.io/upload_images/2086682-768def8419ff52a9.gif?imageMogr2/auto-orient/strip)

公式：

![](http://upload-images.jianshu.io/upload_images/2086682-e4eb4f23fe5e72f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

二阶贝塞尔曲线对应的Path中的方法是quadTo\(\),起点是p0，终点是p2，p1就是运动的控制点



#### 三阶贝塞尔曲线

两个控制点

![](http://upload-images.jianshu.io/upload_images/2086682-5f1a50757e5219b0.gif?imageMogr2/auto-orient/strip)

公式：

![](http://upload-images.jianshu.io/upload_images/2086682-6de4bf743abaf65d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三阶贝塞尔曲线对应Path中的方法是cubicTo\(\), p0是起始点，p3是终点，p1，p2是两个控制点

