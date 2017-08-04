# 理解Canvas

## 简介

> The Canvas class holds the "draw" calls. To draw something, you need 4 basic components: A Bitmap to hold the pixels, a Canvas to host the draw calls \(writing into the bitmap\), a drawing primitive \(e.g. Rect, Path, text, Bitmap\), and a paint \(to describe the colors and styles for the drawing\).

canvas顾名思义“画布”，想要画出一个view，就必须有4个基础步骤

1. 保存像素的bitmap
2. 管理绘制请求的canvas
3. 绘画的原始元素，例如矩形、线、文字、Bitmap
4. 拥有颜色和风格的画笔

虽然canvas为画布，但并不是直接在canvas上画，canvas内部会默认创建一个Bitmap，也可以通过构造方法或者setBitmap\(\)方法传入一个，像素所有的信息是画在这个Bitmap上，然后Bitmap保存在canvas内。

### canvas的创建方法

1. Canvas canvas = new Canvas\(\);
2. Canvas canvas = new Canvas\(bitmap\);创建一个装载画布。构造方法中传入的bitmap存储在所有绘制在canvas中

```java
Bitmap bitmap = Bitmap.createBitmap(100,100,Bitmap.Config.ARGB_8888);
Canvas canvas = new Canvas(bitmap);
canvas.drawColor(Color.BLUE);
```

### 常用方法

| 方法 | 作用 |
| :--- | :--- |
| drawRect\(\) | 画矩形 |
| drawCircle\(\) | 画圆 |
| drawArc\(\) | 画弧 |
| drawRoundRect\(\) | 画圆角矩形 |
| drawBitmap\(\) | 画一个bitmap |
| drawOval\(\) | 画椭圆 |
| drawText\(\) | 画文字 |

### 绘制

View的绘制工作是在onDraw\(\)方法中实现的。

#### 绘制图像

drawBitmap方法是使用频率很高的方法，其构造犯法如下：

```java
drawBitmap(@NonNull Bitmap bitmap, float left, float top, @Nullable Paint paint)
```

* bitmap 要画的目标bitmap

* left 左上角的x轴坐标

* top左上角的y轴坐标

* paint 画笔

```java
    @Override
    protected void onDraw(Canvas canvas) {
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.aaa);
        canvas.drawBitmap(bitmap,0,0,new Paint());
    }
```

```java
drawBitmap(@NonNull Bitmap bitmap, @Nullable Rect src, @NonNull RectF dst,
            @Nullable Paint paint)

drawBitmap(@NonNull Bitmap bitmap, @Nullable Rect src, @NonNull Rect dst,
            @Nullable Paint paint)
```

这两个构造方法的差别在于一个Rect和RectF

* src 用来截取Bitmap局部想要显示的像素块区域，通过构造方法中的四个坐标系点确定范围。这个参数可以为null，为null就是整个bitmap都作为目标资源显示
* dst用来显示的区域，在控件中绘制bitmap的区域，可以实现拉伸或者缩放

rect = new Rect\(x,y,x1,y1\)

* x , X轴开始的坐标点，默认为0
* y，Y轴开始的坐标点
* x1，X轴结束的坐标点，若x1-x大于bitmap的宽度，截取有效区域为bitmap的宽度
* y1，Y轴结束的坐标点

rectF = new RectF\(x,y,x1,y1\)

* x,开始绘制的X轴的坐标点，默认为0
* y，开始绘制的Y轴的坐标点
* x1，结束绘制的X轴的坐标点，若x1-x大于src中x1-x，就是拉伸；小于就是缩放
* y1，结束绘制的Y轴的坐标点



#### 绘制矩形

canvas.drawRect\(float left,float top,float right, float bottom, Paint paint\);

例如：

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawRect(100,100,100,110,paint);
}
```

其他方式：canvas.drawRect\(Rect rect, Paint paint\)和canvas.drawRect\(RectF rect, Paint paint\)

例如：

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Rect rect = new Rect(100,100,200,200);
    canvas.drawRect(rect,paint);
}
```

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    RectF rect = new RectF(100.5f,100.5f,200.5f,200.5f);
    canvas.drawRect(rect,paint);
}
```

其中Rect,和RectF的区别在于前者参数类型是int，后者是参数类型是float

#### 绘制圆形

canvas.drawCircle\(float cx, float cy, float radius, Paint paint\)

绘制圆形就是设置圆心的坐标，然后设置半径。

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    float width = getWidth();
    float height = getHeight();
    float radius = Math.min(width,height)/2;
    canvas.drawCircle(width/2,height/2,radius,paint);
}
```

#### 绘制扇形

canvas.drawArc\(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint\)

canvas.drawArc\(RectF rect, float startAngle, float sweepAngle, boolean useCenter, Paint paint\)

* startAngle : 开始角度
* sweepAngle: 旋转角度
* useCenter: true 是有圆心焦点，false 是没有

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    RectF rect = new RectF(0f,0f,500f,500f);
    canvas.drawArc(rect,0,60,true,paint);
    canvas.drawArc(rect,60,30,true,paint_2);
}
```

#### 绘制bitmap

canvas.drawBitmap\(Bitmap bitmap, float left, float top, Paint paint\)

left:左上角横坐标

top: 左上角纵坐标

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);
    float width = (getWidth()-bitmap.getWidth())/2;
    float height = (getHeight()-bitmap.getHeight())/2;
    canvas.drawBitmap(bitmap,width,height,paint);
}
```

#### 绘制文字

canvas.drawText\(String text, float left, float top, Paint paint\)

```java
/**
 * 绘制文字
 * @param canvas
 */
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawText("HelloWorld",100,100,paint);
}
```

#### 绘制路径

canvas.drawPath\(Path path, Paint paint\)

```java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Path p = new Path();
    p.moveTo(100, 100);
    p.lineTo(200, 50);
    p.lineTo(300, 100);
    p.lineTo(200,400);

    canvas.drawPath(p,paint);
}
```

moveTo:路径绘制的起点

lineTo:连接的点

