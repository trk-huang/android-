# Canvas clip方法学习

* clipPath\(\) 利用Path的方法，可以裁切一块不规则区域的画布
* clipRect\(\)可以裁切出一块矩形画布

clipRect\(\):代码及效果

```java
   Paint mp = new Paint();
        mp.setColor(Color.parseColor("#FF4081"));
        mp.setStyle(Paint.Style.FILL);
        RectF rectF = new RectF(0,0,400,300);

        canvas.drawColor(Color.YELLOW);
        canvas.clipRect(rectF);
        canvas.drawColor(Color.CYAN);
        canvas.drawRect(200,200,600,600,mp);
```

![](/assets/device-2017-08-04-163955.png)

clipPath\(\):代码及效果

```java
        Paint mp = new Paint();
        mp.setColor(Color.parseColor("#FF4081"));
        mp.setStyle(Paint.Style.FILL);
//        RectF rectF = new RectF(0,0,400,300);
        Path path = new Path();
        path.moveTo(0,0);
        path.lineTo(200,250);
        path.lineTo(500,600);
        path.lineTo(0,400);
        path.close();

        canvas.drawColor(Color.YELLOW);
        canvas.clipPath(path);
        canvas.drawColor(Color.CYAN);
        canvas.drawRect(200,200,600,600,mp);
```

![](/assets/device-2017-08-04-164422.png)

