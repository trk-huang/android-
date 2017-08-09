# 理解Matrix之Scale

简介：

对于一个像素点来说，不存在缩放的概念。但是对于一个图像来说，由很多个像素点组成，将每个像素点的坐标按一定的比例进行缩放后，整个图像就能进行缩放了。

计算公式：

```java
x = K1 * x0
y = k1 * y0
```

矩阵形式：

![](http://upload-images.jianshu.io/upload_images/2086682-3a87462eccd2f07c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1240)

k1是缩放比例，负值是无效的，bitmap会不显示；0~1f是缩小；k1&gt;1为放大

#### setScale\(\)缩放方法

```java
1. setScale(float sx, float sy)
2. setScale(float sx, float sy, float px, float py)
```

1. sx是x轴的缩放比例
2. sy是y轴的缩放比例
3. px是缩放中心点的x轴
4. py是缩放中心点的y轴

简单使用，看下效果：

```java
    @Override
    protected void onDraw(Canvas canvas) {
        Matrix matrix = new Matrix();
        matrix.setScale(0.5f,0.5f);
        canvas.drawBitmap(mBitmap,matrix,new Paint());
    }
```

![](/assets/device-2017-08-09-151148.png)

第二个方法的简单使用，看下效果：

```java
    @Override
    protected void onDraw(Canvas canvas) {
        Matrix matrix = new Matrix();
        matrix.setScale(0.5f, 0.5f, getWidth() / 2, getHeight() / 2);
        canvas.drawBitmap(mBitmap, matrix, new Paint());
    }
```

![](/assets/device-2017-08-09-151336.png)此时是以Bitmap为中心，整个bitmap的边缘向中心靠拢。

