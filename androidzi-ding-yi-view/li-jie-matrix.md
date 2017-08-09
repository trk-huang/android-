# 理解Matrix

简介：

Matrix主要是用于对图形的处理。

#### 1.Matrix的变形矩阵

Matrix是一个3\*3的矩阵，每个像素点表达了其坐标的信息，如图：

![](http://upload-images.jianshu.io/upload_images/2086682-0f975e56a75eca12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

处理每个像素点的计算方法：

```
X1 = a * X + b * Y + c
Y1 = d * X + e * Y + f
L  = g * X + h * Y + i
```

一般情况下，g=h=0,i=1,这时，L=g\*X+h\*Y+i恒成立，即：L=i = 1

`Matrix`的初始化矩阵，对角线为`1`，其余为`0`

![](http://upload-images.jianshu.io/upload_images/2086682-6d5f5f98a263109f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Matrix主要对图像可以做4类变换

1. Translate 平移变换
2. Rotate 旋转变换
3. Scale 缩放变换
4. Skew 错切变换

Matrix类中的方法，主要是和这4个变换有关，或者是对计算做一些封装。

作用对象是bitmap，而不是Canvas。

#### 2.Transale 平移变换

![](http://upload-images.jianshu.io/upload_images/2086682-acd2ddc24113e9cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

红点p1平移到白点是p0

坐标点：

```java
x = x1 + x0
y = y1 + y0
```

矩阵形式

![](http://upload-images.jianshu.io/upload_images/2086682-fa85e51e4a228beb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直观的看效果：

![](/assets/QQ20170809-12.png)

上图就是使用了setTranslate\(\)方法，很容易进行平移了。

```java
    @Override
    protected void onDraw(Canvas canvas) {
        Matrix matrix = new Matrix();
        matrix.setTranslate(100f,100f);
        canvas.drawBitmap(mBitmap,matrix,new Paint());
    }
```

同时又不会对canvas的坐标系有影响，不像canvas.translate\(\)。同时，因为bitmap在x轴和y轴上都平移了100f的距离。

```java
x = x1 + 100
y = y1 + 100
```

因此超出canvas的部分图像就不再显示了。

