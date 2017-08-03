# Paint 之 SweepGradient

简介

sweepGradient 即是梯度渐变效果

构造函数：

```java
SweepGradient(float cx, float cy, int color0, int color1)
```

* cx 是旋转点的x轴坐标
* cy是旋转点的y轴坐标
* color0 是旋转点起始颜色
* color1是旋转点结束颜色

```java
SweepGradient(float cx, float cy,
                         int colors[], float positions[])
```

* cx 是旋转点的x轴坐标

* cy是旋转点的y轴坐标

* colors 颜色值数组
* positions 数组中的值有效范围是0f~1f,渐变结束所在的区域的比例



第一种构造函数的代码及效果：

```java
/**
 * Created by huangdaju on 17/8/3.
 */

public class SweepGradientView extends View {

    private Paint mPaint;

    public SweepGradientView(Context context) {
        super(context);
    }

    public SweepGradientView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public SweepGradientView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;

        SweepGradient sweepGradient = new SweepGradient(width, height, Color.RED, Color.YELLOW);
        mPaint.setShader(sweepGradient);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
}
```

![](/assets/device-2017-08-03-174657.png)

第二种构造函数的代码及效果：

```java
    @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;
        int[] colors = new int[]{
                Color.MAGENTA, Color.CYAN, Color.RED
        };
        float[] positions = new float[]{
                0f, 0.5f, 1.0f
        };
        SweepGradient sweepGradient = new SweepGradient(width, height, colors, positions);
        mPaint.setShader(sweepGradient);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
```

![](/assets/device-2017-08-03-174902.png)

