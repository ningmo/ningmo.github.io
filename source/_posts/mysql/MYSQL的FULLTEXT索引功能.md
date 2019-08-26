---
title: MYSQL的FULLTEXT索引功能
categories: []
date: 2013-01-06 11:12:16
---

下文转自某些手册，你可以用这个语句来创建FULLTEXT类型的索引：

CREATE FULLTEXT INDEX INDX_TEXT ON ARTICLE (TITLE,NOTE);


到 3.23.23 时，MySQL 开始支持全文索引和搜索。全文索引在 MySQL 中是一个 FULLTEXT 类型索引。FULLTEXT 索引用于 MyISAM 表，可以在 CREATE TABLE 时或之后使用 ALTER TABLE 或 CREATE INDEX 在 CHAR、VARCHAR 或 TEXT 列上创建。对于大的数据库，将数据装载到一个没有 FULLTEXT 索引的表中，然后再使用 ALTER TABLE (或 CREATE INDEX) 创建索引，这将是非常快的。将数据装载到一个已经有 FULLTEXT 索引的表中，将是非常慢的。

全文搜索通过 MATCH() 函数完成。

mysql> CREATE TABLE articles (
    ->   id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->   title VARCHAR(200),
    ->   body TEXT,
    ->   FULLTEXT (title,body)
    -> );
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO articles VALUES
    -> (NULL,'MySQL Tutorial', 'DBMS stands for DataBase ...'),
    -> (NULL,'How To Use MySQL Efficiently', 'After you went through a ...'),
    -> (NULL,'Optimising MySQL','In this tutorial we will show ...'),
    -> (NULL,'1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    -> (NULL,'MySQL vs. YourSQL', 'In the following database comparison ...'),
    -> (NULL,'MySQL Security', 'When configured properly, MySQL ...');
Query OK, 6 rows affected (0.00 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM articles
    ->          WHERE MATCH (title,body) AGAINST ('database');
+----+-------------------+------------------------------------------+
| id | title             | body                                     |
+----+-------------------+------------------------------------------+
|  5 | MySQL vs. YourSQL | In the following database comparison ... |
|  1 | MySQL Tutorial    | DBMS stands for DataBase ...             |
+----+-------------------+------------------------------------------+
2 rows in set (0.00 sec)
函数 MATCH() 对照一个文本集(包含在一个 FULLTEXT 索引中的一个或多个列的列集)执行一个自然语言搜索一个字符串。搜索字符串做为 AGAINST() 的参数被给定。搜索以忽略字母大小写的方式执行。对于表中的每个记录行，MATCH() 返回一个相关性值。即，在搜索字符串与记录行在 MATCH() 列表中指定的列的文本之间的相似性尺度。

当 MATCH() 被使用在一个 WHERE 子句中时 (参看上面的例子)，返回的记录行被自动地以相关性从高到底的次序排序。相关性值是非负的浮点数字。零相关性意味着不相似。相关性的计算是基于：词在记录行中的数目、在行中唯一词的数目、在集中词的全部数目和包含一个特殊词的文档(记录行)的数目。

它也可以执行一个逻辑模式的搜索。这在下面的章节中被描述。

前面的例子是函数 MATCH() 使用上的一些基本说明。记录行以相似性递减的顺序返回。

下一个示例显示如何检索一个明确的相似性值。如果即没有 WHERE 也没有 ORDER BY 子句，返回行是不排序的。

mysql> SELECT id,MATCH (title,body) AGAINST ('Tutorial') FROM articles;
+----+-----------------------------------------+
| id | MATCH (title,body) AGAINST ('Tutorial') |
+----+-----------------------------------------+
|  1 |                        0.64840710366884 |
|  2 |                                       0 |
|  3 |                        0.66266459031789 |
|  4 |                                       0 |
|  5 |                                       0 |
|  6 |                                       0 |
+----+-----------------------------------------+
6 rows in set (0.00 sec)
下面的示例更复杂一点。查询返回相似性并依然以相似度递减的次序返回记录行。为了完成这个结果，你应该指定 MATCH() 两次。这不会引起附加的开销，因为 MySQL 优化器会注意到两次同样的 MATCH() 调用，并只调用一次全文搜索代码。

mysql> SELECT id, body, MATCH (title,body) AGAINST
    -> ('Security implications of running MySQL as root') AS score
    -> FROM articles WHERE MATCH (title,body) AGAINST
    -> ('Security implications of running MySQL as root');
+----+-------------------------------------+-----------------+
| id | body                                | score           |
+----+-------------------------------------+-----------------+
|  4 | 1. Never run mysqld as root. 2. ... | 1.5055546709332 |
|  6 | When configured properly, MySQL ... |   1.31140957288 |
+----+-------------------------------------+-----------------+
2 rows in set (0.00 sec)
MySQL 使用一个非常简单的剖析器来将文本分隔成词。一个“词”是由文字、数据、“'” 和 “_” 组成的任何字符序列。任何在 stopword 列表上出现的，或太短的(3 个字符或更少的)的 “word” 将被忽略。

在集和查询中的每个合适的词根据其在集与查询中的重要性衡量。这样，一个出现在多个文档中的词将有较低的权重(可能甚至有一个零权重)，因为在这个特定的 集中，它有较低的语义值。否则，如果词是较少的，它将得到一个较高的权重。然后，词的权重将被结合用于计算记录行的相似性。

这样一个技术工作可很好地工作与大的集(实际上，它会小心地与之谐调)。 对于非常小的表，词分类不足以充份地反应它们的语义值，有时这个模式可能产生奇怪的结果。

mysql> SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('MySQL');
Empty set (0.00 sec)
在上面的例子中，搜索词 MySQL 却没有得到任何结果，因为这个词在超过一半的记录行中出现。同样的，它被有效地处理为一个 stopword (即，一个零语义值的词)。这是最理想的行为 -- 一个自然语言的查询不应该从一个 1GB 的表中返回每个次行(second row)。

匹配表中一半记录行的词很少可能找到相关文档。实际上，它可能会发现许多不相关的文档。我们都知道，当我们在互联网上通过搜索引擎试图搜索某些东西时，这会经常发生。因为这个原因，在这个特殊的数据集中，这样的行被设置一个低的语义值。

到 4.0.1 时，MySQL 也可以使用 IN BOOLEAN MODE 修饰语来执行一个逻辑全文搜索。

mysql> SELECT * FROM articles WHERE MATCH (title,body)
    ->     AGAINST ('+MySQL -YourSQL' IN BOOLEAN MODE);
+----+------------------------------+-------------------------------------+
| id | title                        | body                                |
+----+------------------------------+-------------------------------------+
|  1 | MySQL Tutorial               | DBMS stands for DataBase ...        |
|  2 | How To Use MySQL Efficiently | After you went through a ...        |
|  3 | Optimising MySQL             | In this tutorial we will show ...   |
|  4 | 1001 MySQL Tricks            | 1. Never run mysqld as root. 2. ... |
|  6 | MySQL Security               | When configured properly, MySQL ... |
+----+------------------------------+-------------------------------------+
这个查询返回所有包含词 MySQL 的记录行(注意： 50% 的阈值没有使用)，但是它没有包含词 YourSQL。注意，一个逻辑模式的搜索不会自动地以相似值的降序排序记录行。你可以从上面的结果出看得出来，最高的相似值(包含 MySQL 两次的那个) 最列在最后，而不是第一位。一个逻辑全文搜索即使在没有一个 FULLTEXT 索引的情况下也可以工作，然而它 慢 些。

逻辑全文搜索支持下面的操作符：

+
一个领头的加号表示，该词必须出现在每个返回的记录行中。
-
一个领头的减号表示，该词必须不出现在每个返回的记录行中。

缺省的 (当既没有加号也没有负号被指定时)词是随意的，但是包含它的记录行将被排列地更高一点。这个模仿没有 IN BOOLEAN MODE 修饰词的 MATCH() ... AGAINST() 的行为。
< >
这两个操作符用于改变一个词的相似性值的基值。< 操作符减少基值，> 操作符则增加它。参看下面的示例。
( )
圆括号用于对子表达式中的词分组。
~
一个领头的否定号的作用象一个否定操作符，引起行相似性的词的基值为负的。它对标记一个噪声词很有用。一个包含这样的词的记录将被排列得低一点，但是不会被完全的排除，因为这样可以使用 - 操作符。
*
一个星号是截断操作符。不想其它的操作符，它应该被追加到一个词后，不加在前面。
"
短语，被包围在双引号"中，只匹配包含这个短语(字面上的，就好像被键入的)的记录行。
这里是一些示例：

apple banana
找至少包含上面词中的一个的记录行
+apple +juice
... 两个词均在被包含
+apple macintosh
... 包含词 “apple”，但是如果同时包含 “macintosh”，它的排列将更高一些
+apple -macintosh
... 包含 “apple” 但不包含 “macintosh”
+apple +(>pie <strudel)
... 包含 “apple” 和 “pie”，或者包含的是 “apple” 和 “strudel” (以任何次序)，但是 “apple pie” 排列得比 “apple strudel” 要高一点
apple*
... 包含 “apple”，“apples”，“applesauce” 和 “applet”
"some words"
... 可以包含 “some words of wisdom”，但不是 “some noise words”
6.8.1 全文的限制

MATCH() 函数的所有参数必须是从来自于同一张表的列，同时必须是同一个FULLTEXT 索引中的一部分，除非 MATCH() 是 IN BOOLEAN MODE 的。
MATCH() 列列表必须确切地匹配表的某一 FULLTEXT 索引中定义的列列表，除非 MATCH() 是 IN BOOLEAN MODE 的。
AGAINST() 的参数必须是一个常量字符串。
6.8.2 微调 MySQL 全文搜索

不幸地，全文搜索仍然只有很少的用户可调参数，虽然增加一些在 TODO 上排列很高。如果你有一个 MySQL 源码发行(查看章节 2.3 安装一个 MySQL 源码发行)，你可以发挥对全文搜索的更多控制。

注意，全文搜索为最佳的搜索效果，被仔细地调整了。修改默认值的行为，在大多数情况下，只会使搜索结果更糟。不要修改 MySQL 的源代码，除非你知道你在做什么！

被索引的词的最小长度由 MySQL 变量 ft_min_word_len 指定。查看章节 4.5.6.4 SHOW VARIABLES。 将它改为你所希望的值，并重建你的 FULLTEXT 索引。 (这个变量只从 MySQL 4.0 开始被支持)
stopword 列表可以从 ft_stopword_file 变量指定的文件中读取。查看章节 4.5.6.4 SHOW VARIABLES。 在修改了 stopword 列表后，重建你的 FULLTEXT 索引。(这个变量只从 MySQL 4.0.10 开始被支持)
50% 阈值选择由所选择的特殊的衡量模式确定。为了禁止它，修改 `myisam/ftdefs.h' 文件中下面的一行：
#define GWS_IN_USE GWS_PROB
改为：
#define GWS_IN_USE GWS_FREQ
然后重新编译 MySQL。在这种情况下，不需要重建索引。 注意：使用了这个，将严重地减少 MySQL 为 MATCH() 提供足够的相似性值的能力。如果你确实需要搜索这样的公共词，最好使用 IN BOOLEAN MODE 的搜索代替，它不遵守 50% 的阈值。
有时，搜索引擎维护员希望更改使用于逻辑全文搜索的操作符。这些由变量 ft_boolean_syntax 定义。 查看章节 4.5.6.4 SHOW VARIABLES。 然而，这个变量是只读的，它的值在 `myisam/ft_static.c' 中被设置。
对于这些更改，要求你重建你的 FULLTEXT 索引，对于一个 MyISAM 表，最容易的重建索引文件的方式如下面的语句：

mysql> REPAIR TABLE tbl_name QUICK;
6.8.3 全文搜索 TODO

使所有对 FULLTEXT 索引的操作更快
邻近(Proximity)操作符
对 "always-index words" 的支持。他们可以是用户希望视为一个词处理的任意字符串，例如 "C++"、"AS/400"、"TCP/IP"，等等
支持在 MERGE 表中的全文搜索
对多字节字符的支持
依照数据的语言建立 stopword 列表
Stemming (当然，依赖于数据的语言)
Generic user-suppliable UDF preparser.
使模式更加灵活 (通过为 CREATE/ALTER TABLE 中的 FULLTEXT 增加某些可调整参数)
