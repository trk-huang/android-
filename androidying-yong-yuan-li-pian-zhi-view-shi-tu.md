# **Android View视图**

Android View体系是界面编程的核心，他的重要性不亚于Android的四大组件。了解Android View体系，就是要了解View、Android坐标系，视图坐标系。

1. View的简介  
   1. View是Android所有控件的基类，同时也是ViewGroup的基类，看下图：

   ```
   ![](http://upload-images.jianshu.io/upload_images/1417629-5dd667472d3c27d9?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)了解View的层级关系，能让我们更好的掌握View的知识体系，那么我们在界面编程的时候会更加的得心应手
   ```

2. Android坐标系
   1. 在Android中，将屏幕的左上角的顶点作为Android坐标系的原点，这个原点向右是X轴正方向，原点向下是Y轴正方向。
      ![](http://upload-images.jianshu.io/upload_images/1417629-7ba8f74c82658f54?imageMogr2/auto-orient/strip|imageView2/2/w/1240)
      在下文讲到的MotionEvent提供的getRawX\(\)和getRawY\(\)获取的坐标都是Android坐标系的坐标。
3. View坐标系  
   1. 要了解视图坐标系我们只需要看懂一张图就可以了：

   ![](http://upload-images.jianshu.io/upload_images/1417629-6081c0d9534c1647?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

4. View获取自身宽高  
   getHeight\(\)：获取View自身高度  
   getWidth\(\)：获取View自身宽度

5. View自身坐标  
   通过如下方法可以获得View到其父控件（ViewGroup）的距离：

   getTop\(\)：获取View自身顶边到其父布局顶边的距离

   getLeft\(\)：获取View自身左边到其父布局左边的距离

   getRight\(\)：获取View自身右边到其父布局左边的距离

   getBottom\(\)：获取View自身底边到其父布局顶边的距离

6. MotinEvent提供的方法  
   我们看上图那个深蓝色的点，假设就是我们触摸的点，我们知道无论是View还是ViewGroup，最终的点击事件都会由onTouchEvent\(MotionEvent event\)方法来处理，MotionEvent也提供了各种获取焦点坐标的方法：

   getX\(\)：获取点击事件距离控件左边的距离，即视图坐标

   getY\(\)：获取点击事件距离控件顶边的距离，即视图坐标

   getRawX\(\)：获取点击事件距离整个屏幕左边距离，即绝对坐标

   getRawY\(\)：获取点击事件距离整个屏幕顶边的的距离，即绝对坐标



