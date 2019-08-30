---
title: git changelog
date: 2019-08-26 14:15:44
categories: git 
tags: [git,changelog]
---

软件环境安装

```
npm install commitizen -g # 也可在项目根目录本地安装 --save-dev
npm install -g conventional-changelog-cli # 也可在项目根目录本地安装 --save-dev
```

配置
```
commitizen init cz-conventional-changelog --save-dev --save-exact
```

如果出现报错(极大可能是node版本问题 可以切换node版本)
指定适配器

vim .czrc
```
{"path": "cz-conventional-changelog"}

```

指定生成标准angular可选 angular/atom/...

```
conventional-changelog -p angular -i CHANGELOG.md -s

git add .
git cz
```

释义
* feat 新功能
* fix Bug修复
* docs 文档更新
* style 代码的格式，标点符号的更新
* refactor 代码重构
* perf 性能优化
* test 测试更新
* build 构建系统或者包依赖更新
* ci CI 配置，脚本文件等更新
* chore 非 src 或者 测试文件的更新
* revert commit 回退

