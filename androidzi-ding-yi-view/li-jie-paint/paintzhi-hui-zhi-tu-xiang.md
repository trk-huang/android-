# Paint之绘制图像

paint在绘制图像的过程中，重点学习有三个：ColorMatrix、PorterDufferXfermore和Shader

### 1. setColorFilter（ColorFilter colorFilter）

设置颜色过滤器，ColorFilter是一个抽象类，它有三个子类：PorterDuffColorFilter、ColorMatrixColorFilter、LightingColorFilter

### 1.1 PorterDuffColorFilter

指定单一颜色和特色模式的过滤器

构造方法： PorterDuffColorFilter\(int color, PorterDuff.Mode mode\)

* int color 颜色
* PorterDuff.Mode mode 模式

```jav
/**
 * Created by huangdaju on 17/7/30.
 */

public class DrawTestView extends View {

    private int width;
    private int height;
    private int radius = 150;
    private Paint mPaint;

    public DrawTestView(Context context) {
        super(context);
    }

    public DrawTestView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public DrawTestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setStrokeWidth(3);
        mPaint.setColor(Color.RED);
        PorterDuffColorFilter porterDuffColorFilter = new PorterDuffColorFilter(Color.BLUE, PorterDuff.Mode.OVERLAY);
        mPaint.setColorFilter(porterDuffColorFilter);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        width = canvas.getWidth();
        height = canvas.getHeight();
        canvas.drawCircle(width >> 1, height >> 1, radius, mPaint);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }
}
```

### 1.2 LightingColorFilter

> 光照色彩过滤器,一个简单得可以通过光照来影响色彩过滤器

构造方法：LightingColorFilter（int mul, int add）

* mul:全称是colorMultiply意为色彩倍增
* add: 全称是colorAdd意为色彩添加

这两个值都是16进制的色彩值`0xAARRGGBB`

```jav
    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setStrokeWidth(3);
        mPaint.setColor(Color.RED);
//        PorterDuffColorFilter porterDuffColorFilter = new PorterDuffColorFilter(Color.BLUE, PorterDuff.Mode.OVERLAY);
//        mPaint.setColorFilter(porterDuffColorFilter);
        LightingColorFilter lightingColorFilter = new LightingColorFilter(Color.GREEN,Color.BLUE);
        mPaint.setColorFilter(lightingColorFilter);
    }
```

#### 1.3 ColorMatrixColorFilter（ColorMatrix colorMatrix）

ColorMatrix : 色彩矩阵

> A color filter that transforms colors through a 4x5 color matrix. This filter can be used to change the saturation of pixels, convert from YUV to RGB, etc.

通过一个4\*5的色彩矩阵计算进行颜色的过滤器

```java
/**
 * Created by huangdaju on 17/7/30.
 */

public class DrawTestView extends View {

    private int width;
    private int height;
    private int radius = 150;
    private Paint mPaint;

    public DrawTestView(Context context) {
        super(context);
    }

    public DrawTestView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
        initPaint();
    }

    public DrawTestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private void initPaint() {
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mPaint.setStrokeWidth(3);
        mPaint.setColor(Color.RED);
//        PorterDuffColorFilter porterDuffColorFilter = new PorterDuffColorFilter(Color.BLUE, PorterDuff.Mode.OVERLAY);
//        mPaint.setColorFilter(porterDuffColorFilter);
//        LightingColorFilter lightingColorFilter = new LightingColorFilter(Color.GREEN,Color.BLUE);
//        mPaint.setColorFilter(lightingColorFilter);
        ColorMatrix colorMatrix = new ColorMatrix(new float[]{
                1.3F, 0, 0, 0, 0,
                0, 1.5F, 0, 0, 0,
                0, 0, 1.6F, 0, 0,
                0, 0, 0, 1.9F, 0
        });

        ColorMatrixColorFilter colorMatrixColorFilter = new ColorMatrixColorFilter(colorMatrix);
        mPaint.setColorFilter(colorMatrixColorFilter);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        width = canvas.getWidth();
        height = canvas.getHeight();
        canvas.drawCircle(width >> 1, height >> 1, radius, mPaint);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }
}
```

##### ColorMatrix 色彩矩阵

Android中图片处理最常使用到的数据结构是bitmap，包含了整个图片的数据。图片 = 点阵 + 颜色值

* 点阵：一个包含像素的矩阵，每个点对应图片上的像素
* 颜色值：ARGB，分别对应透明度、红、绿、蓝四个通道分量，4个决定了每个像素点的颜色

色彩矩阵的分析

在色彩描述中，一般用色调、饱和度、亮度来描述一个图像

* 色调：物体传递的颜色
* 饱和度：颜色的纯度，从0-100来描述
* 亮度：颜色相对明暗程度

![](http://upload-images.jianshu.io/upload_images/2086682-268bb0802b6dc79d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

用来处理图片色彩

* `abcde`值决定新的颜色值中的`R`—— 红色
* `fghij`值决定新的颜色值中的`G`—— 绿色
* `klmno`值决定新的颜色值中的`B`—— 绿色
* `pqrst`值决定新的颜色值中的`A`—— 透明度
* `ejot`值决定每个分量重的`offset`——偏移量

![](http://upload-images.jianshu.io/upload_images/2086682-ed3998b9d0e3ba3a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

每一个像素都有一个颜色分量矩阵保存颜色`RGBA`值：

矩阵乘法运算：

![](http://upload-images.jianshu.io/upload_images/2086682-91268c1eb5b9884a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

计算过程：

> R1 = a \* R + b \* G + c \* B + d \* A + e
>
> G1 = f \* R + g\*G+h\*B+i\*A+j
>
> B1 = k \* R + l \*G+m\*B+n\*A+o
>
> A1 = p\*R + q\*G+ r\*B+ s\*A + t

设置`a = 1`，`b,c,d,e`都为0，`R1 = R`。同理，`G1 = G`条件为`g = 1, fhij = 0`，然后依次轮推，便可以得到下面这个矩阵：

![](http://upload-images.jianshu.io/upload_images/2086682-7949ff07bc0221fc.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

这个矩阵不会对原有颜色值造成改变，被当做初始矩阵

根据`R = A * C`得知，一般改变颜色值有两种方法可以选择：

改变offset，改变偏移量来改变颜色分量

改变RGBA值的系数，来改变颜色分量

```java
ColorMatrix colorMatrix = new ColorMatrix(new float[]{
                1.3F, 0, 0, 0, 100,
                0, 1.5F, 0, 0, 100,
                0, 0, 1.6F, 0, 0,
                0, 0, 0, 1.9F, 0
        });
```

改变R和G的偏移量，如上述代码中体现，修改红色和绿色值，得到黄色

改变颜色系数:

```java
ColorMatrix colorMatrix = new ColorMatrix(new float[]{
                1.5F, 0, 0, 0, 0,
                0, 1.5F, 0, 0, 0,
                0, 0, 1F, 0, 0,
                0, 0, 0, 1F, 0
        });
```

若小于1，则代表分量减少，红和绿减少，就会偏蓝

大于1代表增加分量，小于1则意味着减少

如下图:

![](/assets/device-2017-07-30-094524.png)

##### 图像的色光属性

setRotate\(int axis, float degrees\) 设置色调

1. axis:颜色编号 0,1,2
2. degrees: 需要处理的值

```java
      //色调
        ColorMatrix rotateMatrix = new ColorMatrix();
        rotateMatrix.setRotate(0,0.8f);//红
        rotateMatrix.setRotate(1,0.8f);//绿
        rotateMatrix.setRotate(2,0.8f);//蓝
```

setSaturation\(float sat\) 设置饱和度

sat的区间值:0~1

```java
      //饱和度
        ColorMatrix saturationMatrix = new ColorMatrix();
        saturationMatrix.setSaturation(0.5f);
```

##### 设置亮度

setScale\(float rScale, float gScale, float bScale, float aScale\)

rScale,gScale,bScale当三个参数的值都相同时，就会显示白色，0代表全黑，1代表原图

如下图:

![](/assets/device-2017-07-30-102954.png)

以下代码结合上面所学的知识:

```java
public class Main2Activity extends AppCompatActivity implements SeekBar.OnSeekBarChangeListener {

    private SeekBar mSeekBar1;
    private SeekBar mSeekBar2;
    private SeekBar mSeekBar3;
    private ImageView iv
    ;

    private float hue;
    private float saturation;
    private float lum;

    private float mid_value = 100f;
    private Bitmap bitmap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);
        initView();
    }

    private void initView() {
        mSeekBar1 = (SeekBar) this.findViewById(R.id.seekBar);
        mSeekBar2 = (SeekBar) this.findViewById(R.id.seekBar2);
        mSeekBar3 = (SeekBar) this.findViewById(R.id.seekBar3);
        iv = (ImageView) this.findViewById(R.id.imageView);
        bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.aaa);
        iv.setImageBitmap(bitmap);
        mSeekBar1.setOnSeekBarChangeListener(this);
        mSeekBar2.setOnSeekBarChangeListener(this);
        mSeekBar3.setOnSeekBarChangeListener(this);
    }

    @Override
    public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
        Log.d("MainActivity2","progress " + progress);
        switch (seekBar.getId()) {
            case R.id.seekBar:
                hue = (progress) / 100f;
                break;
            case R.id.seekBar2:
                saturation = progress / 100f;
                break;
            case R.id.seekBar3:
                lum = progress / 100f;
                break;
        }
        handleImageBitmap();
    }

    private void handleImageBitmap() {
        Bitmap bm = Bitmap.createBitmap(bitmap.getWidth(),bitmap.getHeight(), Bitmap.Config.ARGB_8888);
        Canvas ca = new Canvas(bm);
        Paint pa = new Paint();

        ColorMatrix hueMatrix = new ColorMatrix();
        hueMatrix.setRotate(0,hue);
        hueMatrix.setRotate(1,hue);
        hueMatrix.setRotate(2,hue);

        ColorMatrix saturationMatrix = new ColorMatrix();
        saturationMatrix.setSaturation(saturation);

        ColorMatrix scaleMatrix = new ColorMatrix();
        scaleMatrix.setScale(lum,lum,lum,1);

        ColorMatrix imageMatrix = new ColorMatrix();
        imageMatrix.postConcat(hueMatrix);
        imageMatrix.postConcat(saturationMatrix);
        imageMatrix.postConcat(scaleMatrix);

        pa.setColorFilter(new ColorMatrixColorFilter(imageMatrix));
        ca.drawBitmap(bitmap,0,0,pa);
        iv.setImageBitmap(bm);
    }

    @Override
    public void onStartTrackingTouch(SeekBar seekBar) {

    }

    @Override
    public void onStopTrackingTouch(SeekBar seekBar) {

    }
}
```



