---
title: angularjs ng-repeat checkbox
categories:
  - js
date: 2016-03-03 10:34:28
tags: [angularjs]
---

```
<div class="col-md-3" ng-repeat="user in title.UserList">
    <p class="form-control-static">
        <input type="checkbox" ng-checked="user.Role" ng-model="item.UserId[user.UserId]" ng-true-value="'add_{{user.UserId}}'" ng-false-value="'del_{{user.UserId}}'"/>&nbsp;{{user.UserName}}
    </p>
</div>
```
