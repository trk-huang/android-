# Paint之BitmapShader

### 简介

Shader是android中的着色器。它有5个子类：

* BitmapShader

* LinearGradient

* RadialGradient

* SweepGradient

* ComposeShader

BitmapShader是唯一可以用来对图片进行着色，其他4个是进行渐变、渲染效果。

### BitmapShader

将一个bitmap作为纹理进行绘制，通过设置操作可以进行翻转/镜像操作。

构造方法：BitmapShader（Bitmap bitmap, TileMode tilemodeX, TileMode tilemodeY）

tilemodeX表示在x轴上的模式；

tilemodeY表示在y轴上的模式；

#### TileMode

tilemode表示的是Shader的一个枚举，有三个值：

1. CLAMP：拉伸，图片的最后一个像素，不断重复
2. REPEAT：重复，横向和纵向的重复
3. MIRROR: 镜像，横向不断翻转重复，纵向不断翻转重复



CLAMP效果:

![](/assets/device-2017-08-03-160805.png)

```java
/**
 * Created by huangdaju on 17/8/3.
 */

public class BitmapShaderClampView extends View {

    private Paint mPaint;

    public BitmapShaderClampView(Context context) {
        super(context);
    }

    public BitmapShaderClampView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public BitmapShaderClampView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        final Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.aaa);
        final BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
        mPaint.setShader(bitmapShader);
    }


    @Override
    protected void onDraw(Canvas canvas) {
        float width = getWidth() / 2;
        float height = getHeight() / 2 ;

        canvas.drawCircle(width, height, width / 2, mPaint);

    }
}
```

REPEAT效果:

![](/assets/device-2017-08-03-160853.png)

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

MIRROR效果:

![](/assets/device-2017-08-03-161914.png)

```java
/**
 * Created by huangdaju on 17/8/3.
 */

public class BitmapShaderMirrorView extends View {

    private Paint mPaint;

    public BitmapShaderMirrorView(Context context) {
        super(context);
    }

    public BitmapShaderMirrorView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public BitmapShaderMirrorView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);
        BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.REPEAT, Shader.TileMode.MIRROR);
        mPaint.setShader(bitmapShader);
    }

    @Override
    protected void onDraw(Canvas canvas) {

        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
}
```

### 总结

BitmapShader总是先应用Y轴上的模式后，再应用到X轴上的模式。







