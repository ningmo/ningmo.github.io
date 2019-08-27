---
title: Algorithms_bubble
date: 2016-11-16 19:36:21
tags: [算法,Algorithms,bubble]
categories: Algorithms
---

##### 算法练习：冒泡排序
```
# 排序算法
$arr = [];
for($i=0;$i<10;$i++){
	$arr[] = mt_rand(1,500);
}

echo '<pre>';
echo '元数据';
echo '<br/>';
print_r($arr);
$result =  $arr;
$result2 = $arr;
# 冒泡 desc
for($i=0;$i<count($result)-1;$i++){
	for($j=0;$j<count($result)-$i;$j++){
		if($result[$j] < $result[$j+1]){
			$tmp = $result[$j+1];
			$result[$j+1] = $result[$j];
			$result[$j] = $tmp;
		}
	}
}
echo '<br/>';
echo 'Desc result';
echo '<br/>';
print_r($result);
echo '<br/>';
var_dump($result == sort($arr));

echo '<br/><br/><br/><br/>';
echo 'Asc result<br/>';

# asc
for($i=0;$i<count($result2)-1;$i++){
	for($j=count($result2)-1;$j>=0;$j--){

		if($result2[$j] < $result2[$j-1]){
			$tmp = $result2[$j-1];
			$result2[$j-1] = $result2[$j];
			$result2[$j] = $tmp;
		}
	}
}
echo '<br/>';
print_r($result2);
var_dump($result2 == sort($arr));

# 最佳时间复杂度 O(n)
```