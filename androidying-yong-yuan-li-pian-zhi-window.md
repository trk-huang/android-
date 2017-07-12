Android之window

## Window的概念 {#window的概念}

Window表示的是一个窗口的概念，它是一个抽象类，它的**具体实现是PhoneWindow**。创建一个Window需要通过WindowManager来完成。WindowManager是外界访问Window的入口，Window的具体实现位于WindowManagerService，**WindowManager与WindowManagerService的交互是一个IPC过程**。Android中所有的View都是Window来呈现的，不管是Activity、Toast还是Dialog，它们的视图都是附加到Window上的，因此**Window是View的直接管理者**。  
View的事件分发机制中的事件传递：单击事件由Activity内部的Window -&gt; Decor View -&gt; View

## WindowManager {#windowmanager}

windowManager：window的管理器，一个window对应一个windowManager。它继承与ViewManager接口，接口中定义了三个方法：add，updateViewLayout,removeView。所以想要对window的View进行操作的话，是必须要用到WindowManager

windowManagerservice：它是由SystemServer孵化出来的一个Service.它继承于IWindowManager.Stub，马上反应出就知道它需要进程间通信的。它通信的主要目标就还是WindowManager，就是要堆屏幕里面的窗口进行一一管理。

PhoneWindow：它是Window的唯一实现类。它里面包含了一个最顶层的DecorView。所有的view都是存在于DecorView下面的。PhoneWindow里面继承了一个Callback接口，该接口里面包含了大量事件处理方法。分析的点击事件，就是对这些方法进行分析的。

RootViewImpl：View的测量，布局，绘制就是在它的performTraversals方法里开始的。

### 1. 添加view到Window示例 {#1-添加view到window示例}

使用WindowManager添加一个view到Window  
**自定义浮窗 需要权限android.permission.SYSTEM\_ALERT\_WINDOW**

```java
/**
     * 显示浮窗
     * @param content   要填充的文本内容
     * @param layoutId   用于创建窗体View的布局
     */
    public  void show(String content,int layoutId) {
          wm = (WindowManager)context.getSystemService(Context.WINDOW_SERVICE);
        params = new WindowManager.LayoutParams();
        screenHeight = AppInfoUtils.getScreenSize(context).height;
        screenWidth = AppInfoUtils.getScreenSize(context).width;
        // 加载布局
        view = View.inflate(mContext, layoutId, null);
        // 设置浮窗params属性
        params.width = WindowManager.LayoutParams.WRAP_CONTENT;
        params.height = WindowManager.LayoutParams.WRAP_CONTENT;
        params.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE;
        params.type = WindowManager.LayoutParams.TYPE_PHONE;
        params.gravity = Gravity.TOP+Gravity.LEFT; // 将重心设置为左上方
        params.format = PixelFormat.TRANSLUCENT;    // 半透明
        params.x = sp.getInt("startX", 0);      // 设置显示位置
        params.y = sp.getInt("startY", 0);
        TextView tvLocation =  (TextView) view.findViewById(R.id.tv_toast_location);
        tvLocation.setText(content);
        // 将View添加到窗体管理器
        wm.addView(view, params);
    }
```

### 1.参数分析 {#1-添加view到window示例}

LayoutParams.Flags参数表示window的属性，通过设置它的选项可以控制window的显示特性。如下几种常见选项：

FLAG_NOT_\_FOCUSABLE

> ```
> 不许获得焦点
> ```

**FLAG\_NOT\_TOUCHABLE**

```
不接受触摸屏事件
```

**FLAG\_NOT\_TOUCH\_MODAL**

```
当窗口可以获得焦点（没有设置 FLAG_NOT_FOCUSALBE 选项）时，仍然将窗口范围之外的点设备事件（鼠标、触摸屏）发送给后面的窗口处理。否则它将独占所有的点设备事件，而不管它们是不是发生在窗口范围内。

```

**FLAG\_SHOW\_WHEN\_LOCKED**

```
当屏幕锁定时，窗口可以被看到。这使得应用程序窗口优先于锁屏界面。可配合FLAG_KEEP_SCREEN_ON选项点亮屏幕并直接显示在锁屏界面之前。可使用FLAG_DISMISS_KEYGUARD选项直接解除非加锁的锁屏状态。此选项只用于最顶层的全屏幕窗口。

```

**FLAG\_DIM\_BEHIND**。

```
  窗口之后的内容变暗

```

**FLAG\_BLUR\_BEHIND**

```
窗口之后的内容变模糊。

```

_**2、Type参数表示Window的类型**_

有3种主要类型：

1\)Application\_windows （应用Window）：

```
    值在 FIRST_APPLICATION_WINDOW 和 LAST_APPLICATION_WINDOW 之间。
    是通常的、顶层的应用程序窗口。必须将 token 设置成 activity 的 token 。  

```

2\)Sub\_windows （子Window）：

```
    取值在 FIRST_SUB_WINDOW 和 LAST_SUB_WINDOW 之间。与顶层窗口相关联，token 必须设置为它所附着的宿主窗口的 token。

```

3\)System\_windows （系统Window）：

```
    取值在 FIRST_SYSTEM_WINDOW 和 LAST_SYSTEM_WINDOW 之间。
```

3、Window的层次

```
每个Window都有对应的z-ordered，层次大的会覆盖到层次小的Window上面。在三类Window中应用Window的层级范围在1~99之间，子Window的范围在1000~1999之间，系统Window的层级范围在2000~2999之间。
```

要使Window位于所有Window的最顶层，采用较大的层级即可，系统Window的层级是最大的，一般选用TYPE\_SYSTEM\_OVERLAY或TYPE\_SYSTEM\_ERROR，同时要声明权限android.permission.SYSTEM\_ALERT\_WINDOW。如下示例

```
    params.type = WindowManager.LayoutParams.TYPE_SYSTEM_ERROR;

```

4、WindowManager提供的常用方法  
WindowManager继承自ViewManager，提供了添加view、删除view和更新view，这三个方法都是定义在ViewManager。

```java
public interface ViewManager
{
    /**
     * @param view The view to be added to this window.
     * @param params The LayoutParams to assign to view.
     */
    public void addView(View view, ViewGroup.LayoutParams params);
    public void updateViewLayout(View view, ViewGroup.LayoutParams params);
    public void removeView(View view);
}

```



## Activity,Window,View之间的关系 {#window的概念}

Android系统上所有的UI类都是建立在View和ViewGroup这两个的基础之上。所有的view的子类成为“Widget”，所有的ViewGroup的子类成为layout。view和viewgroup之间采用组合设计模式。可以使得“部分-整体”同等对待。

而当我们运行程序的时候，有一个setContentView\(\)方法，Activity其实不是显示视图（直观上感觉是它），实际上Activity调用了PhoneWindow的setContentView\(\)方法，然后加载视图，将视图放到这个Window上，而Activity其实构造的时候初始化的是Window（PhoneWindow），Activity其实是个控制单元，即可视的人机交互界面。

打个比喻：Activity是一个工人，它来控制Window；Window是一面显示屏，用来显示信息；View就是要显示在显示屏上的信息，这些View都是层层重叠在一起（通过infalte\(\)和addView\(\)）放到Window显示屏上的。而LayoutInfalter就是用来生成View的一个工具，XML布局文件就是用来生成View的原料

再来说说代码中具体的执行流程

setContentView\(R.layout.main\)其实就是下面内容。（注释掉本行执行下面的代码可以更直观）

getWindow\(\).setContentView\(LayoutInflater.from\(this\).inflate\(R.layout.main, null\)\)

即运行程序后，Activity会调用PhoneWindow的setContentView\(\)来生成一个Window，而此时的setContentView就是那个最底层的View。然后通过LayoutInflater.infalte\(\)方法加载布局生成View对象并通过addView\(\)方法添加到Window上，（一层一层的叠加到Window上）

所以，Activity其实不是显示视图，View才是真正的显示视图

> 注：一个Activity构造的时候只能初始化一个Window\(PhoneWindow\)，另外这个PhoneWindow有一个”ViewRoot”，这个”ViewRoot”是一个View活ViewGroup，是最初始的跟视图，然后通过addView方法将View一个个层叠到ViewRoot上，这些层叠的View最终放在Window这个载体上面







