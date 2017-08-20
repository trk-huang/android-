# 自定义涂鸦工具

功能需求：

1. 设置画笔形状\(直径、矩形、圆形、实心矩形、实心圆形\)
2. 设置画笔粗细\(细、中、粗\)
3. 设置画笔颜色\(红、黄、蓝\)
4. 重置
5. 保存



首先看一下大致的类图：

![](/assets/aaaa.png)这个自定义view采用策略模式设计，首先定义画笔的行为类ActionStrategy：

看一下源码

```java
/**
 * Created by huangdaju on 17/8/18.
 */

public abstract class ActionStrategy {

    public int color;

    public ActionStrategy() {
        color = Color.BLACK;
    }

    public ActionStrategy(int color) {
        this.color = color;
    }

    public abstract void draw(Canvas canvas);

    public abstract void move(float fx, float fy);
}

```

ActionStrategy作为一个抽象类，提供抽象的draw，move方法给具体策略类。

二、ActionFactory作为提供策略的工厂类

看一下源码

```java
/**
 * Created by huangdaju on 17/8/18.
 */

public class ActionFactory {

    public static ActionStrategy choiceAction(Type type, float fx, float fy, int color, int size) {
        ActionStrategy actionStrategy = null;
        switch (type) {
            case PATH:
                actionStrategy = new PathAction(fx, fy, size, color);
                break;
            case CIRCLE:
                actionStrategy = new CircleAction();
                break;
            case SQUARE:
                actionStrategy = new SquareAction();
                break;
            case FILL_CIRCLE:
                actionStrategy = new FillCircleAction();
                break;
            case FILL_SQUARE:
                actionStrategy = new FillSquareAction();
                break;
        }
        return actionStrategy;
    }
}
```

三、再看一下具体策略提供类，例如ActionPath

```java
/**
 * Created by huangdaju on 17/8/18.
 */

public class PathAction extends ActionStrategy {

    private Path mPath;
    private int size;


    public PathAction() {
        mPath = new Path();
        this.size = 1;
    }

    public PathAction(float fx, float fy, int size, int color) {
        super(color);
        mPath = new Path();
        this.size = size;
        mPath.moveTo(fx, fy);
        mPath.lineTo(fx, fy);
    }

    @Override
    public void draw(Canvas canvas) {
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setDither(true);
        paint.setColor(color);
        paint.setStrokeWidth(size);
        paint.setStyle(Paint.Style.STROKE);
        canvas.drawPath(mPath, paint);
    }

    @Override
    public void move(float fx, float fy) {
        mPath.lineTo(fx, fy);
    }
}

```

其中我们定义PathAction,添加了path和size两个成员变量。size作为画笔的粗细，path作为曲线的轨迹。draw\(\)方法创建了Paint，设置paint的属性，再让canvas绘制。

实现具体自定义的DoodleView

```java
/**
 * Created by huangdaju on 17/8/18.
 */

public class DoodleView extends SurfaceView implements SurfaceHolder.Callback {

    private SurfaceHolder mSurfaceHolder = null;

    private ActionStrategy mActionStrategy;
    private int currentColor = Color.BLACK;
    private int currentSize = 5;
    private Paint mPaint;
    private List<ActionStrategy> mActionStrategyList;
    private Type type = Type.PATH;
    private Bitmap mBitmap;
    private Context mContext;

    public DoodleView(Context context) {
        super(context);
    }

    public DoodleView(Context context, AttributeSet attrs) {
        super(context, attrs);
        mContext = context.getApplicationContext();
        initView();
    }

    public DoodleView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }


    private void initView() {
        mSurfaceHolder = this.getHolder();
        mSurfaceHolder.addCallback(this);
        this.setFocusable(true);
        mPaint = new Paint();
        mPaint.setColor(Color.WHITE);
        mPaint.setStrokeWidth(currentSize);
    }

    @Override
    public void surfaceCreated(SurfaceHolder holder) {
        Canvas canvas = holder.lockCanvas();
        canvas.drawColor(currentColor);
        mSurfaceHolder.unlockCanvasAndPost(canvas);
        mActionStrategyList = new ArrayList<>();
    }

    @Override
    public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {

    }

    @Override
    public void surfaceDestroyed(SurfaceHolder holder) {

    }


    public void setCurrentColor(int currentColor) {
        this.currentColor = currentColor;
    }

    public void setCurrentSize(int currentSize) {
        this.currentSize = currentSize;
    }

    public void setType(Type type) {
        this.type = type;
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int action = event.getAction();
        float touchX = event.getRawX();
        float touchY = event.getRawY();

        switch (action) {
            case MotionEvent.ACTION_CANCEL:
                return false;
            case MotionEvent.ACTION_DOWN:
                setCurrentAction(touchX, touchY);
                break;
            case MotionEvent.ACTION_UP:
                mActionStrategyList.add(mActionStrategy);
                mActionStrategy = null;
                break;
            case MotionEvent.ACTION_MOVE:
                Canvas canvas = mSurfaceHolder.lockCanvas();
                canvas.drawColor(Color.WHITE);
                for (ActionStrategy actionStrategy : mActionStrategyList) {
                    actionStrategy.draw(canvas);
                }
                mActionStrategy.move(touchX, touchY);
                mActionStrategy.draw(canvas);
                mSurfaceHolder.unlockCanvasAndPost(canvas);
                break;
        }
        return super.onTouchEvent(event);
    }

    public String saveBitmap() {
//        String path = Environment.getExternalStorageDirectory().getAbsolutePath() +  + System.currentTimeMillis() + ".png";
        String path = Utils.savePath(mContext);
//


        savePicByPng(this.getBitmap(), path);
        return path;
    }

    private void savePicByPng(Bitmap ba, String filePath) {
        FileOutputStream fileoutputStream = null;
        try {
            fileoutputStream = new FileOutputStream(filePath);
            if (null != fileoutputStream) {
                ba.compress(Bitmap.CompressFormat.PNG, 90, fileoutputStream);
                fileoutputStream.flush();
                fileoutputStream.close();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public Bitmap getBitmap() {
        mBitmap = Bitmap.createBitmap(getWidth(), getHeight(), Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(mBitmap);
        doDraw(canvas);
        return mBitmap;
    }

    private void doDraw(Canvas canvas) {
        canvas.drawColor(Color.TRANSPARENT);
        for (ActionStrategy actionStrategy : mActionStrategyList) {
            actionStrategy.draw(canvas);
        }
        canvas.drawBitmap(mBitmap, 0, 0, mPaint);
    }

    private void setCurrentAction(float touchX, float touchY) {
        mActionStrategy = ActionFactory.choiceAction(Type.PATH, touchX, touchY, currentColor, currentSize);
    }

    public void reset() {
        if (mActionStrategyList != null && mActionStrategyList.size() > 0) {
            mActionStrategyList.clear();
            Canvas ca = mSurfaceHolder.lockCanvas();
            ca.drawColor(Color.WHITE);
            for (ActionStrategy actionStrategy : mActionStrategyList) {
                actionStrategy.draw(ca);
            }
            mSurfaceHolder.unlockCanvasAndPost(ca);
        }
    }
}
```



