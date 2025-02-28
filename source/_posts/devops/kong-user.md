---
title: kong使用
date: 2018-04-04 21:20:08
tags: kong
categories: devops
---


kong 总体流程
创建接口-给接口添加验证插件-创建用户-给用户创建key

给接口添加验证插件
curl -X POST http://10.10.107.103:8001/apis/qzh/plugins -d "name=key-auth"
{"api_id":"1b545af7-42ff-4143-8c70-5a4c323d1a41","id":"8e104fce-5ceb-4a38-b6bf-f8fcfb8ad048","created_at":1522828616000,"enabled":true,"name":"key-auth","config":{"hide_credentials":false,"anonymous":"","key_names":["apikey"],"key_in_body":false}}

给用户创建key
curl -X POST http://10.10.107.103:8001/consumers/qzhuser/key-auth -d "key=qzhkey"
{"created_at":1522828821000,"consumer_id":"f2531e2f-c6ba-4f97-94e4-a9454b70bb95","key":"qzhkey","id":"33fc0bff-7f23-43bc-bf60-e6114f0b04ab"}
