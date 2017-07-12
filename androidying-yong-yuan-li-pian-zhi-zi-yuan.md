# **Android 资源**

> 小记：Android相比于iOS有一个非常让开发者头疼的问题，即：Android设备种类繁多，硬件配置五花八门，碎片化太严重。特别是层出不穷的屏幕分辨率和尺寸。往往对一款应用程序的UI界面有非常大的影响-----应用程序在屏幕A中可以完美显示，而在屏幕B中则可能是一团糟。鉴于此，应用开发商往往需要同时适配多大上百款主流设备才敢将产品投入市场。

既然Android无法像iOS一样从本质上去规避这种情况。因此，如何尽可能减小它所带来的影响就成了开发人员首先考虑的问题。这也是本章的目的-------我们将结合Android系统提供的通用适配方案和准则，来学习如何在项目开发初期将这些“烦人”的问题掌握在可控范围内。

我们先从理论层来分析适配的过程，如下图：

![](/assets/未知设备.png)

* 未知设备，对于应用程序来讲，它将运行于什么设备上面是一个未知数。
* 提取设备参数，对于不同的设备，它的设备参数也可能不一样。
* 资源池，一个应用可选的资源。
* 最佳适配原则，整个资源匹配流程的核心，也是最终的仲裁者。



## 资源类型

| 类型 | 存放位置 | 访问方式 | 描述 |
| :--- | :--- | :--- | :--- |
| 动画资源\(Animation Recources\) | res/anim;res/drawable | R.anim.xxx;R.drawable.xxx | 动画实现 |
| 状态颜色资源\(Color State list Resource\) | res/color/ | R.color.xxx | 定义各种配色方案，并可以根据程序的view状态自动切换颜色 |
| 图形资源（DrawableResouces） | res/drawable/ | R.drawable.xxx | 各种图片文件 |
| 布局资源\(Layout Resource\) | res/layout/ | R.layout.xxx | 布局文件 |
| 菜单资源\(Menu Resource\) | res/menu/ | R.menu.xxx | 菜单资源 |
| 字符串资源\(String Resource\) | res/values/ | R.string.xxx，R.array.string\_array\_name | 字符串和字符串数组 |
| 样式资源\(Style Resource\) | res/values/ | R.style.xxx | 定义UI外观 |
| 原始资源（Raw Resource\) | res/values/ | R.raw.xxx;Resources.openRawResource函数打开 | 存放原始格式的资源，如音频文件，也可以将这类文件放在asset/目录下，这时资源不会被分配id值，可以用AssetManager来访问 |
| 其他资源 | res/values | R.bool.xxx,R.integer.xxx;R.color.xxx | 用于定义各种变量值，如整型、布尔型、颜色、尺寸等 |



状态颜色资源

我们可以为某个View对象在不同的状态下配置不同的颜色。

操作步骤：

1. 创建一个状态颜色文件，文件结构是由一个selector，内嵌若干个item，每个item描述一个View的状态所对应的颜色。

```java
<selector xmlns:android="https://schemas.android.com/apk/res/android">
    <item ...>
    <item ...>
</selector>
```

## **最佳资源的匹配流程**

![](/assets/排除不符合设备为前配置的资源选项 .png)

## 屏幕适配

屏幕适配是确保Android应用程序兼容性一个至关重要的环节。

1. 屏幕适配的重要参数
   1. 屏幕分辨率QVGA，VGA，
   2. 屏幕密度，ldpi，mdpi，hdpi，xhdpi
   3. 屏幕尺寸，small，normal，large，xlarge
   4. 屏幕宽高比
   5. 屏幕方向
   6. 设备独立像素
      1. px=dp \* \(dpi / 160\)
2. 如何适配多屏幕
   1. Android系统为当前屏幕配置选择最佳资源的流程
   2. 为不同的屏幕尺寸提供多种layout布局
   3. 为不同的屏幕方向提供多种layout布局
   4. 为不同的屏幕密度提供相应的图片资源
   5. 显示声明应用程序支持的屏幕类型
   6. 支持特定的屏幕
   7. 布局时的注意事项



