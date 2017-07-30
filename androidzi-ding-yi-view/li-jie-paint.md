# 理解Paint

## 简介

> The Paint class holds the styles and color-information about how to draw geometries,text and bitmaps

画笔能够拿到，所要绘制的几何图形、文字或者Bitmap颜色、风格等信息。

通俗的讲：自定义view就是绘制文字和绘制图像。

Paint的构造方法：

public Paint\(\);   // create a new paint with default settings

public Paint\(int flags\); //create a new paint with specified flags

public Paint \(Paint paint\); // create a new paint ,initialized with attributes in the specified paint paramter



设置画笔样式:

Paint.style.FILL 实心铺满

Paint.style.STROKE stroke

设置flags:

ANTI\__ALIAS_\_FLAG：抗锯齿

DITHER\_FLAG：防抖动



## 常用属性方法

#### 绘制文字

| 方法 | 作用 |
| :--- | :--- |
| setColor\(\) | 设置画笔颜色 |
| setStrokeWidth\(\) | 设置画笔粗细 |
| setTextSkewX\(\) | 设置倾斜，负为右斜；正为左斜 |
| setARGB\(\) | 设置颜色 |
| setTextSize\(\) | 设置绘制文字大小 |
| setFakeBoldText\(\) | 设置是否粗体 |
| setTextAlign\(\) | 设置文字对齐，left，center，right |
| setunderlineText\(\) | 设置下划线 |
| setStyle\(\) | 设置画笔样式，FILL,STROKE,FILL\_AND\)STORKE |
| setTypeface\(\) | 设置typeface对象 |

例如普通效果:

![](/assets/device-2017-07-30-004319.png)

```java
/**
 * Created by huangdaju on 17/7/29.
 */

public class TextTestView extends View {

    private Paint mPaint;
    private String text = "Hello world!";

    public TextTestView(Context context) {
        this(context, null);
    }

    public TextTestView(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs, 0);
        initPaint();    }

    public TextTestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    /**
     * 初始化画笔
     */
    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setColor(Color.parseColor("#FFF000"));
        mPaint.setTextSize(90);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
//        float width = mPaint.measureText(text);
//        float x = (getWidth() - width) /2f;
        canvas.drawText(text, 0, getHeight() >> 1, mPaint);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }


}
```

文字水平居中:

![](/assets/device-2017-07-30-004435.png)

```java
/**
 * Created by huangdaju on 17/7/29.
 */

public class TextTestView extends View {

    private Paint mPaint;
    private String text = "Hello world!";

    public TextTestView(Context context) {
        this(context, null);
    }

    public TextTestView(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs, 0);
        initPaint();
    }

    public TextTestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    /**
     * 初始化画笔
     */
    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setColor(Color.parseColor("#FFF000"));
        mPaint.setTextSize(90);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        float width = mPaint.measureText(text);
        float x = (getWidth() - width) /2f;
        canvas.drawText(text, 0, getHeight() >> 1, mPaint);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }


}
```

利用measureText\(\)方法获取文字的宽度，从而简单地获取到x轴的起始坐标，达到文字水平居中

垂直居中：

在android中，和文字高度相关的信息都存在FontMetrics对象中。

FontMetrics是Paint的一个静态内部类，

```java
/**
     * Class that describes the various metrics for a font at a given text size.
     * Remember, Y values increase going down, so those values will be positive,
     * and values that measure distances going up will be negative. This class
     * is returned by getFontMetrics().
     */
    public static class FontMetrics {
        /**
         * The maximum distance above the baseline for the tallest glyph in
         * the font at a given text size.
         */
        public float   top;
        /**
         * The recommended distance above the baseline for singled spaced text.
         */
        public float   ascent;
        /**
         * The recommended distance below the baseline for singled spaced text.
         */
        public float   descent;
        /**
         * The maximum distance below the baseline for the lowest glyph in
         * the font at a given text size.
         */
        public float   bottom;
        /**
         * The recommended additional space to add between lines of text.
         */
        public float   leading;
    }

```

![](http://upload-images.jianshu.io/upload_images/2086682-e53c745b6e65da44.gif?imageMogr2/auto-orient/strip)

在`FontMetrics`有五个`float`类型值:

* `leading` 留给文字音标符号的距离

* `ascent` 从`baseline`线到最高的字母顶点到距离，负值

* `top` 从`baseline`线到字母最高点的距离加上`ascent`

* `descent` 从`baseline`线到字母最低点到距离

* `bottom` 和`top`类似，系统为一些极少数符号留下的空间。`top`和`bottom`总会比`ascent`和`descent`大一点的就是这些少到忽略的特殊符号

`baseline`上为负，下为正。可以理解为文字坐标系中的`x`轴

##### 确定文字`Y`轴的坐标

![](http://upload-images.jianshu.io/upload_images/2086682-9efd6da03a4ddb94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```java
/**
 * Created by huangdaju on 17/7/29.
 */

public class TextTestView extends View {

    private Paint mPaint;
    private String text = "Hello world!";

    public TextTestView(Context context) {
        this(context, null);
    }

    public TextTestView(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs, 0);
        initPaint();
    }

    public TextTestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    /**
     * 初始化画笔
     */
    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setColor(Color.parseColor("#FFF000"));
        mPaint.setTextSize(90);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        float width = mPaint.measureText(text);
        Paint.FontMetrics metrics = mPaint.getFontMetrics();
        float x = (getWidth() - width) / 2f;
        float y = getHeight() / 2 + (Math.abs(metrics.ascent - metrics.descent) / 2f);
        canvas.drawText(text, x, y, mPaint);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }


}

```

#### 绘制图像

| 方法 | 作用 |
| :--- | :--- |
| setDither\(\) | 设置抖动处理 |
| setAlpha\(\) | 设置透明度 |
| setAntiAlias\(\) | 是否抗锯齿 |
| setFilterBitmap\(\) | 是否开启优化Bitmap |
| setColorFilter\(\) | 设置颜色过滤 |
| setMaskFilter\(\) | 设置滤镜 |
| setShader\(\) | 设置图像渐变效果 |
| setStrokeJoin\(\) | 设置图像结合方式 |
| setXfermode\(\) | 设置图像重叠效果 |
| setPathEffect\(\) | 设置路径效果 |
| reset\(\) | 恢复默认设置 |



