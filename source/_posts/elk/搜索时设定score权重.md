---
title: 搜索时定义score权重
tags: [elasticsearch, score]
date: 2019-08-27 22:18:00
categories: [elasticsearch]
---

匹配任一条件并设定其匹配的score权重
```php
$nameBoost = [];
$nameBoost['bool']['should'][] = ['terms'=>['name'=>$nameOrderSet,"boost"=> 500]];
$nameBoost['bool']['should'][] = ['wildcard'=>['name'=>['value'=>$name.'?',"boost"=> 200]]];
$nameBoost['bool']['should'][] = ['wildcard'=>['name'=>['value'=>$name.'*',"boost"=> 100]]];
$params['body']['query']['bool']['must'][]  = $boolQuery;
```