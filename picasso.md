# Picasso

简介：

picasso是Square公司开源的一个Android图片缓存框架，地址是[http://square.github.io/picasso/](http://square.github.io/picasso/),可以实现图片下载和缓存功能。仅仅需要一行代码就可以完成图片的异步加载：

```java
Picasso.with(context).load("http://www.jcodecraeer.com/uploads/20140731/67391406772378.png").into(imageView);
```

API类似于Retrofit的api，是不。

它解决了Android加载图片时的一些常见问题：

1. 在adapter中需要取消已经不在视野范围的ImageView图片资源的加载，否则会导致图片错位，Picasso已经解决了这个问题。

2. 使用复杂的图片压缩转换，来尽可能减少内存的消耗

3. 自带内存和硬盘的二次缓存



