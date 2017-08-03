# Paint之LinearGradient

简介

Lineargradient是可以画出线性渐变的功能。

它的构造方法

```java
LinearGradient(float x0, float y0, float x1, float y1,int color0, inti color1, TitleMode title)
```

* x0绘制起始点的x轴的坐标
* y0绘制起始点的y轴的坐标
* x1绘制结束点的x轴的坐标
* y1绘制结束点的y轴的坐标
* color0起始点的颜色
* color1结束点的颜色
* title 瓷砖模式



```java
LinearGradient(float x0, float y0, float x1, float y1, int colors[], float positions[], TitleMode title)
```

* x0绘制起始点的x轴的坐标

* y0绘制起始点的y轴的坐标
* x1绘制结束点的x轴的坐标
* y1绘制结束点的y轴的坐标
* colors 颜色int值数组
* positions 数组中的值有效范围是0f~1f,渐变结束所在的区域的比例，1f的结束区域与x1,y1有关
* title 瓷砖模式

先看第一种构造函数，看下面的代码及效果:

```java
/**
 * Created by huangdaju on 17/8/3.
 */

public class BitmapShaderRepeatView extends View {

    private Paint mPaint;

    public BitmapShaderRepeatView(Context context) {
        super(context);
    }

    public BitmapShaderRepeatView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public BitmapShaderRepeatView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        final Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);
        final BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT);
        mPaint.setShader(bitmapShader);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
}
```

效果图：

![](/assets/device-2017-08-03-164227.png)





从上图中的效果很容易理解，控件从\(0，0\)点到屏幕右下角d 两种颜色渐变

但是看不出TitleMode的效果，原因是控件的大小和渐变的大小是一样的；如果把渐变的效果减小到屏幕的一半，就能体现TitleMode的效果了。

```java

  @Override
    protected void onDraw(Canvas canvas) {
        int color0 = Color.parseColor("#ffffff");
        int color1 = Color.parseColor("#343445");
        LinearGradient linearGradient = new LinearGradient(0, 0, getWidth() / 2, getHeight() / 2, color0, color1, Shader.TileMode.REPEAT);
        mPaint.setShader(linearGradient);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
```

效果图：

![](/assets/device-2017-08-03-165239.png)





第二种构造函数，看代码及效果：

```java
 @Override
    protected void onDraw(Canvas canvas) {

        int[] colors = new int[]{
                Color.MAGENTA, Color.CYAN, Color.RED
        };
        float[] positions = new float[]{
                0f, 0.5f, 1.0f
        };
        LinearGradient linearGradient = new LinearGradient(0, 0, getWidth(), getHeight(), colors, positions, Shader.TileMode.REPEAT);
        mPaint.setShader(linearGradient);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
```

效果图:

![](/assets/device-2017-08-03-170119.png)

