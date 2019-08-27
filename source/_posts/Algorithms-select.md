---
title: Algorithms_select
date: 2016-11-16 20:51:18
tags: [Algorithms,select,算法]
categories: Algorithms
---

##### 算法练习：选择排序
```
$arr = [];
for($i=0;$i<10;$i++){
	$arr[] = mt_rand(1,500);
}
$arr2 = $arr;
echo '<pre><br/>元数据:';
print_r($arr);
echo '<br/>';
$l = 0;
$result = [];

# asc
for($i=0;$i<count($arr);$i++){
	$min = $arr[$i];
	for($j=$i;$j<count($arr)-1;$j++){
		if($min>$arr[$j+1]){
			$tmp = $arr[$j+1];
			$arr[$j+1] = $min;
			$min = $tmp;
		}
		$l += 1;
	}
	$result[$i] = $min;
	
}
echo 'Asc Result:<br/>';
print_r($result);
echo 'length:'.$l.'<br/>';


$l = 0;
# desc
$result2 = [];
for($i=0;$i<count($arr2);$i++){
	$max = $arr2[$i];
	for($j=$i;$j<count($arr2)-1;$j++){
		if($max<$arr2[$j+1]){
			$tmp = $arr2[$j+1];
			$arr2[$j+1] = $max;
			$max = $tmp;
		}
		$l += 1;
	}
	$result2[count($arr2)-1-$i] = $max;
	
}
echo 'Desc Result:<br/>';
print_r($result2);
echo 'length:'.$l.'<br/>';
```