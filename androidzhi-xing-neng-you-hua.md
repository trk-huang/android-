# Android 性能优化

1. Android性能问题简介--性能测试中有两个指标来判断应用的性能问题
   1. 响应时间
      1. 指从用户操作开始到系统给用户以正确反馈的时间。一般包括逻辑处理时间+网络请求时间+展示时间。对于非网络类的应用是没有网络请求时间。展示时间就是网页或者App界面渲染时间。

      响应时间是用户对性能的最直接体验感受
   2. TPS（Transaction Per Second）
      1. 为每秒的事务数，即系统的吞吐量的指标，通常所说的性能问题就是指响应时间过长、系统吞吐量过低。对移动开发来说，性能问题还包括电量、内存使用这两类较特殊情况。
2. 性能调优方式
           明白了性能问题之后，就能明白性能优化，其实就是优化应用的响应时间和提高TPS，实现方式有三大
   1. 降低执行时间
      1. 利用多线程并发或分布式提高TPS
      2. 缓存（包括对象缓存、IO缓存、网络缓存等）
      3. 数据结构和算法优化
      4. 性能更优的底层接口调用，如JNI实现
      5. 逻辑优化
      6. 需求优化
   2. 同步改异步，利用多线程提高TPS
   3. 提前或延迟操作，错峰提高TPS

   对数据库优化、布局优化、Java代码优化部分、网络请求优化都可以归纳到以上三类中。




