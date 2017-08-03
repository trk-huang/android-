# Paint 之 RadialGradient

简介

RadialGradient 能画出向光束一样的渐变效果

它的构造函数是:

```java
RadialGradient(float centerX, float centerY, float radius,
            int centerColor, int edgeColor, @NonNull TileMode tileMode)
```

* centerX 渐变中心点的x轴坐标
* centerY 渐变中心点的y轴坐标
* radius 渐变区域的半径
* centerColor 渐变中心的颜色值
* edgeColor 渐变边缘的颜色值

```java
RadialGradient(float centerX, float centerY, float radius,
               @NonNull int colors[], @Nullable float stops[], @NonNull TileMode tileMode)
```

* centerX 渐变中心点的x轴坐标

* centerY 渐变中心点的y轴坐标

* radius 渐变区域的半径
* colors 颜色值数组
* stops 数组中的值有效范围是0f~1f,渐变结束所在的区域的比例



第一种构造函数的代码及效果：

```java
/**
 * Created by huangdaju on 17/8/3.
 */

public class RadialGradientView extends View {

    private Paint mPaint;

    public RadialGradientView(Context context) {
        super(context);
    }

    public RadialGradientView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public RadialGradientView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;
        int radius = width;
        RadialGradient radialGradient = new RadialGradient(width, height, radius, Color.RED, Color.BLUE, Shader.TileMode.REPEAT);
        mPaint.setShader(radialGradient);
        canvas.drawCircle(width, height, width, mPaint);
    }
}
```

![](/assets/device-2017-08-03-172151.png)

第二种构造函数的代码及效果:

```java
@Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;
        int radius = width;
        int[] colors = new int[]{
                Color.MAGENTA, Color.CYAN, Color.RED
        };
        float[] positions = new float[]{
                0f, 0.5f, 1.0f
        };
        RadialGradient radialGradient = new RadialGradient(width, height, radius, colors, positions, Shader.TileMode.REPEAT);

        mPaint.setShader(radialGradient);
        canvas.drawCircle(width, height, width, mPaint);
    }
```

![](/assets/device-2017-08-03-173451.png)

