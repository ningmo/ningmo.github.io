---
title: mysql分表hash方式
categories: []
date: 2013-01-31 10:37:51
---

HASH分区主要用来确保数据在预先确定数目的分区中平均分布。在RANGE和LIST分区中，必须明确指定一个给定的列值或列值集合应该保存在哪个分区中；而在HASH分区中，MySQL 自动完成这些工作，你所要做的只是基于将要被哈希的列值指定一个列值或表达式，以及指定被分区的表将要被分割成的分区数量。
要使用HASH分区来分割一个表，要在CREATE TABLE 语句上添加一个“PARTITION BY HASH (expr)”子句，其中“expr”是一个返回一个整数的表达式。它可以仅仅是字段类型为MySQL 整型的一列的名字。此外，你很可能需要在后面再添加一个“PARTITIONS num”子句，其中num 是一个非负的整数，它表示表将要被分割成分区的数量。
例如，下面的语句创建了一个使用基于“store_id”列进行 哈希处理的表，该表被分成了4个分区：
```
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY HASH(store_id)
PARTITIONS 4；

```
如果没有包括一个PARTITIONS子句，那么分区的数量将默认为1。 例外： 对于NDB Cluster（簇）表，默认的分区数量将与簇数据节点的数量相同，这种修正可能是考虑任何MAX_ROWS 设置，以便确保所有的行都能合适地插入到分区中。（参见第17章：MySQL簇）。
如果在关键字“PARTITIONS”后面没有加上分区的数量，将会出现语法错误。
“expr”还可以是一个返回一个整数的SQL表达式。例如，也许你想基于雇用雇员的年份来进行分区。这可以通过下面的语句来实现：
```
CREATE TABLE employees (
    id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT,
    store_id INT
)
PARTITION BY HASH(YEAR(hired))
PARTITIONS 4；
```

“expr”还可以是MySQL 中有效的任何函数或其他表达式，只要它们返回一个既非常数、也非随机数的整数。（换句话说，它既是变化的但又是确定的）。但是应当记住，每当插入或更新（或者可能删除）一行，这个表达式都要计算一次；这意味着非常复杂的表达式可能会引起性能问题，尤其是在执行同时影响大量行的运算（例如批量插入）的时候。
最有效率的哈希函数是只对单个表列进行计算，并且它的值随列值进行一致地增大或减小，因为这考虑了在分区范围上的“修剪”。也就是说，表达式值和它所基于的列的值变化越接近，MySQL就可以越有效地使用该表达式来进行HASH分区。
例如，“date_col” 是一个DATE（日期）类型的列，那么表达式TO_DAYS(date_col)就可以说是随列“date_col”值的变化而发生直接的变化，因为列“date_col”值的每个变化，表达式的值也将发生与之一致的变化。而表达式YEAR(date_col)的变化就没有表达式TO_DAYS(date_col)那么直接，因为不是列“date_col”每次可能的改变都能使表达式YEAR(date_col)发生同等的改变。即便如此，表达式YEAR(date_col)也还是一个用于 哈希函数的、好的候选表达式，因为它随列date_col的一部分发生直接变化，并且列date_col的变化不可能引起表达式YEAR(date_col)不成比例的变化。
作为对照，假定有一个类型为整型（INT）的、列名为“int_col”的列。现在考虑表达式“POW(5-int_col,3) + 6”。这对于哈希函数就是一个不好的选择，因为“int_col”值的变化并不能保证表达式产生成比例的变化。列 “int_col”的值发生一个给定数目的变化，可能会引起表达式的值产生一个很大不同的变化。例如，把列“int_col”的值从5变为6，表达式的值将产生“－1”的改变，但是把列“int_col”的值从6变为7时，表达式的值将产生“－7”的变化。
换句话说，如果列值与表达式值之比的曲线图越接近由等式“y=nx（其中n为非零的常数）描绘出的直线，则该表达式越适合于 哈希。这是因为，表达式的非线性越严重，分区中数据产生非均衡分布的趋势也将越严重。
理论上讲，对于涉及到多列的表达式，“修剪（pruning）”也是可能的，但是要确定哪些适于 哈希是非常困难和耗时的。基于这个原因，实际上不推荐使用涉及到多列的哈希表达式。
当使用了“PARTITION BY HASH”时，MySQL将基于用户函数结果的模数来确定使用哪个编号的分区。换句话，对于一个表达式“expr”，将要保存记录的分区编号为N ，其中“N = MOD(expr, num)”。例如，假定表t1 定义如下，它有4个分区：
CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATE)
    PARTITION BY HASH( YEAR(col3) )
    PARTITIONS 4；
如果插入一个col3列值为'2005-09-15'的记录到表t1中，那么保存该条记录的分区确定如下：
MOD(YEAR('2005-09-01'),4)
=  MOD(2005,4)
=  1
MySQL 5.1 还支持一个被称为“linear hashing（线性哈希功能）”的变量，它使用一个更加复杂的算法来确定新行插入到已经分区了的表中的位置。关于这种算法的描述，请参见18.2.3.1节，“LINEAR HASH分区” 。
每当插入或更新一条记录，用户函数都要计算一次。当删除记录时，用户函数也可能要进行计算，这取决于所处的环境。
注释：如果将要分区的表有一个唯一的键，那么用来作为HASH用户函数的自变数或者主键的column_list的自变数的任意列都必须是那个键的一部分。

