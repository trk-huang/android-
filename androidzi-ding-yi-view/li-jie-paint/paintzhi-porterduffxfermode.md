# Paint之PorterDuffXfermode

### 简介

porterDuffXfermode 类似数学中的交集和并集。用来两个图像之间的混合显示模式。"dst"是下层，就是先画的图像；“src”是上层，是后画的图像。



构造方法：PorterDuffXfermode\(PorterDuff.Mode mode\)。mode是一个枚举类型，共有18种类型。

![](/assets/20160110111111072)



测试代码：自定义CircleImageView

```java
/**
 * Created by huangdaju on 17/7/30.
 */

public class CircleImageView extends ImageView {

    private Paint mpaint;

    public CircleImageView(Context context) {
        this(context, null);
    }

    public CircleImageView(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public CircleImageView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initPaint();
    }

    private void initPaint() {
        mpaint = new Paint(Paint.ANTI_ALIAS_FLAG);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        BitmapDrawable drawable = (BitmapDrawable) getDrawable();
        if (drawable != null) {
            Bitmap bitmap = drawable.getBitmap();
            drawCircleBitmap(canvas, bitmap);
        }
    }

    private void drawCircleBitmap(Canvas canvas, Bitmap bitmap) {
        int sc = canvas.saveLayer(0, 0, getWidth(), getHeight(), null, Canvas.ALL_SAVE_FLAG);
        //绘制dst层
        int width = getWidth();
        int height = getHeight();
        float raduis = (width > height ? height : width) >> 1 ;
        float x = width >> 1, y = height >> 1;
        Log.d("MainActivity3", " x , " + x + "y, " + y + "radius, " + raduis);
        canvas.drawCircle(x, y, raduis, mpaint);
        PorterDuffXfermode mode = new PorterDuffXfermode(PorterDuff.Mode.SRC_IN);
        mpaint.setXfermode(mode);

        //src层
        float srcX = (width - bitmap.getWidth()) >> 1;
        float srcY = (height - bitmap.getHeight()) >> 1;
        Log.d("MainActivity3", " srcX , " + srcX + "srcY, " + srcY);
        canvas.drawBitmap(bitmap, srcX, srcY, mpaint);

        mpaint.setXfermode(null);
        canvas.restoreToCount(sc);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }


}
```





