# 性能优化之数据库优化

1. 索引
   简单来说，索引就像书本的目录，目录可以快速找到所在页数，数据库中索引可以帮助快速找到数据，而不用全表扫描，合适的索引可以大大地提高数据库查询效率。
   1. 优点：大大加快了数据库检索速度，例如单表查询、连表查询、分组查询、排序查询
   2. 缺点：索引的创建和维护存在消耗、索引会占用物理空间，且随着数据量的增加而增加。在对数据库进行增删改时需要维护索引，所以会对增删改的性能存在影响。
   3. 分类：
      a. 直接创建索引和间接创建索引 直接创建: 使用sql语句创建，Android中可以在SQLiteOpenHelper的onCreate或是onUpgrade中直接excuSql创建语句，语句如
      | 1 | CREATEINDEXmycolumn\_indexONmytable\(myclumn\) |
      | :--- | :--- |


      间接创建: 定义主键约束或者唯一性键约束，可以间接创建索引，主键默认为唯一索引。

      b. 普通索引和唯一性索引  
      普通索引：

      | 1 | CREATEINDEXmycolumn\_indexONmytable\(myclumn\) |
      | :--- | :--- |


      唯一性索引：保证在索引列中的全部数据是唯一的，对聚簇索引和非聚簇索引都可以使用，语句为

      | 1 | CREATEUNIQUECOUSTEREDINDEXmyclumn\_cindexONmytable\(mycolumn\) |
      | :--- | :--- |




      c. 单个索引和复合索引

      单个索引：索引建立语句中仅包含单个字段，如上面的普通索引和唯一性索引创建示例。  
      复合索引：又叫组合索引，在索引建立语句中同时包含多个字段，语句如：

      | 1 | CREATEINDEXname\_indexONusername\(firstname,lastname\) |
      | :--- | :--- |


      其中firstname为前导列。

      d. 聚簇索引和非聚簇索引\(聚集索引，群集索引\)  
      聚簇索引：物理索引，与基表的物理顺序相同，数据值的顺序总是按照顺序排列，语句为：

      | 1 | CREATECLUSTEREDINDEXmycolumn\_cindexONmytable\(mycolumn\)WITHALLOW\_DUP\_ROW |
      | :--- | :--- |


      其中WITH ALLOW\_DUP\_ROW表示允许有重复记录的聚簇索引

      非聚簇索引：

      | 1 | CREATEUNCLUSTEREDINDEXmycolumn\_cindexONmytable\(mycolumn\) |
      | :--- | :--- |


      索引默认为非聚簇索引

   4. 使用场景
      1. 在上面讲到了优缺点，那么肯定会对何时使用索引既有点明白又有点糊涂吧，那么下面总结下：

         a.  当某字段数据更新频率较低，查询频率较高，经常有范围查询\(&gt;, &lt;, =, &gt;=, &lt;=\)或order by、group by发生时建议使用索引。并且选择度越大，建索引越有优势，这里选择度指一个字段中唯一值的数量/总的数量。

         b.  经常同时存取多列，且每列都含有重复值可考虑建立复合索引
   5. 索引使用规则
      a.  对于复合索引，把使用最频繁的列做为前导列\(索引中第一个字段\)。如果查询时前导列不在查询条件中则该复合索引不会被使用。  
      如create unique index PK\_GRADE\_CLASS on student \(grade, class\)  
      select \* from student where class = 2未使用到索引  
      select \* from dept where grade = 3使用到了索引

      b.  避免对索引列进行计算，对where子句列的任何计算如果不能被编译优化，都会导致查询时索引失效  
      select \* from student where tochar\(grade\)=’2′  
      c.  比较值避免使用NULL  
      d.  多表查询时要注意是选择合适的表做为内表。连接条件要充份考虑带有索引的表、行数多的表，内外表的选择可由公式：外层表中的匹配行数\*内层表中每一次查找的次数确定，乘积最小为最佳方案。实际多表操作在被实际执行前，查询优化器会根据连接条件，列出几组可能的连接方案并从中找出系统开销最小的最佳方案。

      e.  查询列与索引列次序一致  
      f.  用多表连接代替EXISTS子句  
      g.  把过滤记录数最多的条件放在最前面  
      h.  善于使用存储过程，它使sql变得更加灵活和高效\(Sqlite不支持存储过程

   6. 索引检验
      1. 建立了索引，对于某条sql语句是否使用到了索引可以通过执行计划查看是否用到了索引。
2. 使用事务
   1. 使用事务的两大好处是原子提交和更优性能。  
      **\(1\) 原子提交**  
      原则提交意味着同一事务内的所有修改要么都完成要么都不做，如果某个修改失败，会自动回滚使得所有修改不生效。



      **\(2\) 更优性能**  
      Sqlite默认会为每个插入、更新操作创建一个事务，并且在每次插入、更新后立即提交。

      这样如果连续插入100次数据实际是创建事务-&gt;执行语句-&gt;提交这个过程被重复执行了100次。如果我们显示的创建事务-&gt;执行100条语句-&gt;提交会使得这个创建事务和提交这个过程只做一次，通过这种一次性事务可以使得性能大幅提升。尤其当数据库位于sd卡时，时间上能节省两个数量级左右。

      Sqlte显示使用事务，示例代码如下：

```java
public void insertWithOneTransaction() {
    SQLiteDatabase db = sqliteOpenHelper.getWritableDatabase();
    // Begins a transaction
    db.beginTransaction();
    try {
        // your sqls
        for (int i = 0; i < 100; i++) {
            db.insert(yourTableName, null, value);
        }
 
        // marks the current transaction as successful
        db.setTransactionSuccessful();
    } catch (Exception e) {
        // process it
        e.printStackTrace();
    } finally {
        // end a transaction
        db.endTransaction();
    }
}
```



1. 其他针对Sqlite的优化
   1. **语句的拼接使用StringBuilder代替String**

      这个就不多说了，简单的string相加会导致创建多个临时对象消耗性能。StringBuilder的空间预分配性能好得多。如果你对字符串的长度有大致了解，如100字符左右，可以直接new StringBuilder\(128\)指定初始大小，减少空间不够时的再次分配。

   2. **查询时返回更少的结果集及更少的字段。**

      查询时只取需要的字段和结果集，更多的结果集会消耗更多的时间及内存，更多的字段会导致更多的内存消耗。

   3. **少用cursor.getColumnIndex**
2. 异步线程

Sqlite是常用于嵌入式开发中的关系型数据库，完全开源。  
与Web常用的数据库Mysql、Oracle db、sql server不同，Sqlite是一个内嵌式的数据库，数据库服务器就在你的程序中，无需网络配置和管理，数据库服务器端和客户端运行在同一进程内，减少了网络访问的消耗，简化了数据库管理。不过Sqlite在并发、数据库大小、网络方面存在局限性，并且为表级锁，所以也没必要多线程操作。

Android中数据不多时表查询可能耗时不多，不会导致anr，不过大于100ms时同样会让用户感觉到延时和卡顿，可以放在线程中运行，但sqlite在并发方面存在局限，多线程控制较麻烦，这时候可使用单线程池，在任务中执行db操作，通过handler返回结果和ui线程交互，既不会影响UI线程，同时也能防止并发带来的异常。



