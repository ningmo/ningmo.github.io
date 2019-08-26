---
date: 2013-03-23T22:46:55

title: 读取树状数据的方法

---

读取树状数据的方法
一般在读取用“邻接列表算法”组织的数据时，需要使用递归逐层读取
或者读取数据到数组，然后用递归或非递归的方法再行处理
这里介绍一种边读边生成“树状”数组的方法

mysql_connect();
···
//测试数据
$sql =<<< SQL
select * from (
  select '1' as id, '0' as pid, 'Food' as title
  union all select '2', '1', 'Fruit'
  union all select '3', '2', 'Red'
  union all select '4', '3', 'Cherry'
  union all select '5', '2', 'Yellow'
  union all select '6', '5', 'Banana'
  union all select '7', '1', 'Meat'
  union all select '8', '7', 'Beef'
  union all select '9', '7', 'Pork'
  ) t
  order by pid, id
SQL;

$rs = mysql_query($sql);

$res = array(); //结果数组
$ind = array(); //索引数组
while($row = mysql_fetch_assoc($rs)) {
  list($id, $pid) = array_values($row);
  $ind[$id] = $row;
  if(isset($ind[$pid])) $ind[$pid]['child'][$id] =& $ind[$id]; //构造索引
  if($pid == 0) $res[$id] =& $ind[$id]; //转存根节点组
}

echo '<xmp>' . print_r($res, 1);
···

···
Array
(
    [1] => Array
        (
            [id] => 1
            [pid] => 0
            [title] => Food
            [child] => Array
                (
                    [2] => Array
                        (
                            [id] => 2
                            [pid] => 1
                            [title] => Fruit
                            [child] => Array
                                (
                                    [3] => Array
                                        (
                                            [id] => 3
                                            [pid] => 2
                                            [title] => Red
                                            [child] => Array
                                                (
                                                    [4] => Array
                                                        (
                                                            [id] => 4
                                                            [pid] => 3
                                                            [title] => Cherry
                                                        )

                                                )

                                        )

                                    [5] => Array
                                        (
                                            [id] => 5
                                            [pid] => 2
                                            [title] => Yellow
                                            [child] => Array
                                                (
                                                    [6] => Array
                                                        (
                                                            [id] => 6
                                                            [pid] => 5
                                                            [title] => Banana
                                                        )

                                                )

                                        )

                                )

                        )

                    [7] => Array
                        (
                            [id] => 7
                            [pid] => 1
                            [title] => Meat
                            [child] => Array
                                (
                                    [8] => Array
                                        (
                                            [id] => 8
                                            [pid] => 7
                                            [title] => Beef
                                        )

                                    [9] => Array
                                        (
                                            [id] => 9
                                            [pid] => 7
                                            [title] => Pork
                                        )

                                )

                        )

                )

        )

)
···
来自：http://bbs.csdn.net/topics/390364669
