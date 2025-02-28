---
title: ob混淆截取学习记录
categories: [js]
tags: [ob混淆]
date: 2022-09-01 15:54:20
---

随手找了一个网站 做ob反混淆学习 经过几天尝试 附上记录

网站请求过程：第一次请求网站  网站先返回一个js脚本(抓包可见) 执行后生成cookie reload后 返回正常页面 后续在有cookie的情况下 直接返回页面 不再返回js脚本

#### 源码分析
代码格式化之后 经网上搜索相关内容后 将代码按功能分成三部分
第一部分：声明关键词数组 并将数组倒序  并提供_0x59af(x)实现变量的调用 后面代码大量使用此方法
第二部分：防调试部分 用浏览器对象去判断当前环境 让代码进入死循环防止调试
第三部分：业务实现 cookie生成等

#### 代码替换
从网上找了一个python代码 编译js代码 运行_0x59af(x) 用其结果 替换代码中的调用 来让代码易读
将替换后的js代码放到html中 后面可以用浏览器打开进行调试
```python
# -*- coding: utf-8 -*-
'''
无法100%还原为源代码，只能修改变量和方法名，增加可读性
'''
import re
import execjs
func_js = open('exe.js').read()  # 截取第一部分代码写入exe.js

# 1.编译解混淆函数到node.js环境中
js_func_name = '_0x59af'  # 混淆js中函数定义的名称
ctx = execjs.compile(func_js)
# 2.正则匹配出所有需要替换的函数
with open('source.html',encoding='utf-8') as f1:
    js = f1.read()

be_replaced_func_set = set(re.findall(js_func_name + "\([\s\S]+?\)",js))

# 3.循环遍历进行替换
for be_replaced_func in be_replaced_func_set:
    args_tuple = re.findall("\(([\s\S]+?)\)", be_replaced_func)[0]
    args0 = eval(args_tuple.split(',')[0]) # 截取参数
    res = ctx.call(js_func_name, args0) # 调用参数，获取返回值
    js = js.replace(be_replaced_func, "'" + res + "'")
    print('{} 替换'.format(res))

with open('code.html', 'w',encoding='utf-8') as f2: # 重写JS输出
    js = f2.write(js)
```

#### 调试与修改
格式化后的代码可能会影响调试 所以尽量用格式化前的代码进行调试 浏览器内进行格式化
浏览器内打断点进行调试 找到设置cookie的地方 将cookie值返回
涉及到document window的地方 要再代码前声明 方面后面的调用
将修改后的代码 修改成可调用模块或者可执行的js 拼接参数执行
```js
const res = (new Function(`${jsString};return execJS("${a}","${b}")`))();
```

#### 后记
有的网站此类js代码是动态生成的 上面的方法就需要改一改
在代码前面 加上执行环境变量声明  代码尾部加上执行代码
如果动态代码有比较明显的共同点 也可以replace 将其中的关键方法导出 给到window上 在尾部就可以直接调用了
