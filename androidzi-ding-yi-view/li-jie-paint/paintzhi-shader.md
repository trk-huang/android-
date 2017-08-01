# Paint之Shader



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



