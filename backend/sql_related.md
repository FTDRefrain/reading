## MySQL相关内容

1. 数据类型相关

   1. INT(11)：里面限制的是显示数值的长度而不是合法的范围；指定zerofill后就是0填充，填充到前面
   2. DECIMAL可以用来存比BIGINT还要大的数也可以存储精确小数，FLOAT要指定精确位数，且浮点是近似计算
   3. VARCHAR，存储可变长的字符串，比定长节省空间；里面有1或者2个额外字节记录字符串长度，长度小于255是1位，否则2位；超出会截取
   4. CHAR，定长的字符串，会往小缩不会增长，使用空格填充剩余位置方便比较；
   5. BLOB/TEXT两个字符类型在查找的时候使用的是临时表， 少用
   6. 枚举：即固定类别的内容，如男女，内部存储的其实是整数
   7. 日期：TIMESTAMP效率高，微妙存储的话要使用BIGINT(通过扩大放进去，取的时候除以倍数)

2. 数据表引擎

   1. InnoDB表引擎：事务性引擎，数据存在共享表空间，主键查询性能最高；磁盘读取会在内存构建hash，插入数据则构建插入缓冲区；支持行级锁和外键
   2. MyISAM表引擎：没有事务和行级锁，有全文检索

3. 索引

   1. 减少扫描的数据量，避免排序和临时表，随机I/O变成顺序I/O，提高了查询速度

   2. 降低了写速度，占用磁盘

   3. 类型：主键和唯一索引

      1. 主键只有一个但是唯一有很多；主键可以和外键构成完整性约束
      2. 组合索引：多个列组合在一起构建索引

   4. 构建原则：

      1. where后面的内容构建
      2. 索引列的基数越大效果越好
      3. 字符串索引应该要限制长度

   5. 索引的注意事项

      1. 前缀原则：即key(a, b, c)对于`WHERE b=1 AND c=1`就没有用

      2. like里面的问题，后面表示包含的wang的就不行，因为%在前

         ```sql
         where name like "%wang%"
         ```

      3. 对于扫描少的会自动优化，因为本身还有多扫描索引的一个步骤；

      4. or查询的时候两测都有索引才会生效

      5. 查询没有类型转换，即类型不对索引失效，但是值能查到

4. SQL语句编写

   1. 关联更新操作，即将A表中和B表ID相同的东西的c1和c2都更新；

      ```sql
      UPDATE A,B SET A.c1 = B.c1, A.c2 = B.c2 WHERE A.id = B.id
      // 或者下面的方式
      UPDATE A INNER JOIN B ON A.id = B.id SET A.c1 = B.c1, A.c2 = B.c2 WHERE A.id = B.id
      ```

   2. 关联查询

      1. 交叉连接：`SELECT * FROM A,B(,C)` 或者是CROSS JOIN，很少使用

      2. 内连接：INNER JOIN，找的是多表中符合条件的数据集合

         1. 语句实例如下

            ```sql
            SELECT * FROM A INNER JOIN B ON A.id=B.id
            SELECT * FROM A,B WHERE A.id=B.id
            ```

         2. 三类，等值，不等值和自连接

            ```sql
            // 自连接就是自己和自己连接查询
            SELECT * FROM A T1 JOIN A T2 ON T1.id=T2.pid
            ```

      3. 外连接

         1. 左外，以左表为主，先查询出左表，按照ON后的关联条件匹配右表，没有匹配到就使用null填充
         2. 右外，类似上面

      4. 联合查询：即UNION将所有的结果合并起来

      5. 全连接：左外 UNION 右外结果

      6. 嵌套查询，即一条的结果作为一条的输入

         ```sql
         SELECT * FROM A WHERE id IN (SELECT id FROM B)
         ```

      7. 根据team和match表，返回主队，结果，客队和时间；代码如下

         ```sql
         // 一步一步写，先写基础的然后进行关联操作
         SELECT hostTeamId, matchResult, guestTeamId, matchTime FROM match WHERE matchTime between "2016-7" and "2016-9"
         // 补上关联操作
         SELECT t1.teamName, m.matchResult, t2.teamName, m.matchTime FROM match as m LEFT JOIN team as t1 on m.hostTeamId = t1.teamId, LEFT JOIN team as t2 on m.guestTeamId = t2.teamId WHERE m.matchTime between "2016-7" and "2016-9"
         ```

5. 查询性能和优化

   1. profile进行每条语句运行时间的检查















