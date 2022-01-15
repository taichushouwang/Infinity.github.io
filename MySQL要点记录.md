title: MySQL要点记录
categories: MySQL
tags: MySQL
date: 2015-05-29 14:20:46
---

1. 记得使用**mysqli_close()**

2. `ORDER BY col1 DESC, col2 ASC;`先按**col1**降序，再按**col2**升序。

3. mysql语句, 不支持php的多维数组, 在SELECT语句中的限定条件变量**$array[0]['col']**，要变成`$col=$array[0]; $query="select * from table where col = $col[col]"`, 而`$query="select * from table where col = $array[0]['col']"`是不管用的

4. 规范化数据库：
（1）确保列具有原子性（所有字段值都是不可分解的原子值）
（2）每个表都有自己的主键
（3）确保非键的列不相互依赖（非必须遵守）（不懂）

5. 确保应用安全，应对特定用户使用不同的数据库的管理权限

6. 查看数据库错误，使用var_dump(mysqli_error());

7. 对于一些自增变量，可以使用语句`alter table table_name AUTO_INCREMENT=n;`来初始化递增的初始值。

8. 建表时，需要对每个变量进行注释，尤其是在WampSever中，注释之后，可以很方便的查看变量的意义

9. 外键：外键用于与另一张表的关联。是能确定另一张表记录的字段用于保持数据的一致性。

需要学习的：
1. 数据库命名规则
2. SQL查询返回的结果的规范性应该是怎样的
	（1）到底要怎么封装数据库的函数
3. 查询语句"like %string% "
4. 对查询结果进行分页
	（1）用PHP对数据库查询进行分页显示
	（2）用jQuery进行自动加载
5. 将PHP和HTML分开写，是用Smarty模版写，还是用jQuery动态加载，两者的限制分别是什么