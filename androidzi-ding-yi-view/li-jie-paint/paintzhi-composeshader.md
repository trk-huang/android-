# Paint 之 ComposeShader 

简介

ComposeShader 即混合渐变，可以把前面两种渐变混合成另外一种渐变效果



构造函数：



```java
ComposeShader(Shader shaderA, Shader shaderB, Xfermode mode)
```



```java
ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)
```

两种构造函数，前两个参数都是相同的，都是一个着色器，区别在于第三个参数，第一种构造函数是PorterDuffXfermode，

第二种构造函数是PorterDuff.Mode



第一种构造函数的代码及效果

```java
    @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;

        LinearGradient linearGradient = new LinearGradient(0, 0, width, height, Color.RED, Color.YELLOW, Shader.TileMode.REPEAT);
        RadialGradient radialGradient = new RadialGradient(width, height, width, Color.BLUE, Color.CYAN, Shader.TileMode.REPEAT);
        ComposeShader composeShader = new ComposeShader(linearGradient, radialGradient, new PorterDuffXfermode(PorterDuff.Mode.SCREEN));
        mPaint.setShader(composeShader);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
```

![](/assets/device-2017-08-03-180512.png)

第二种构造函数的代码及效果



```java
 @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth() / 2;
        int height = getHeight() / 2;

        LinearGradient linearGradient = new LinearGradient(0, 0, width, height, Color.RED, Color.YELLOW, Shader.TileMode.CLAMP);
        RadialGradient radialGradient = new RadialGradient(width, height, width, Color.BLUE, Color.CYAN, Shader.TileMode.REPEAT);
        ComposeShader composeShader = new ComposeShader(linearGradient, radialGradient, PorterDuff.Mode.SCREEN);
        mPaint.setShader(composeShader);
        canvas.drawRect(0, 0, getWidth(), getHeight(), mPaint);
    }
```

![](/assets/device-2017-08-03-180658.png)

从上图的结果对比，两个构造函数生成的效果无差。

