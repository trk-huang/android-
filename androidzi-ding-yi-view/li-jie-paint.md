# 理解Paint

## 简介

> The Paint class holds the styles and color-information about how to draw geometries,text and bitmaps

画笔能够拿到，所要绘制的几何图形、文字或者Bitmap颜色、风格等信息。

通俗的讲：自定义view就是绘制文字和绘制图像。

Paint的构造方法：

public Paint\(\);   // create a new paint with default settings

public Paint\(int flags\); //create a new paint with specified flags

public Paint \(Paint paint\); // create a new paint ,initialized with attributes in the specified paint paramter

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





