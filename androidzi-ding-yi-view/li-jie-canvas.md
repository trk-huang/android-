# 理解Canvas

## 简介

> The Canvas class holds the "draw" calls. To draw something, you need 4 basic components: A Bitmap to hold the pixels, a Canvas to host the draw calls \(writing into the bitmap\), a drawing primitive \(e.g. Rect, Path, text, Bitmap\), and a paint \(to describe the colors and styles for the drawing\).

canvas顾名思义“画布”，想要画出一个view，就必须有4个基础步骤

1. 保存像素的bitmap
2. 管理绘制请求的canvas
3. 绘画的原始元素，例如矩形、线、文字、Bitmap
4. 拥有颜色和风格的画笔

### canvas的创建方法

1. Canvas canvas = new Canvas\(\);
2. Canvas canvas = new Canvas\(bitmap\);创建一个装载画布。构造方法中传入的bitmap存储在所有绘制在canvas中

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

