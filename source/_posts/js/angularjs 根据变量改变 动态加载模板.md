---
title: angularjs 根据变量改变 动态加载模板
categories:
  - js
date: 2015-10-12 13:56:46
---

directive
```
return {
        restrict: 'E',
        replace: true,
        templateUrl: 'app/view/order.html',
        link: function (scope, element, attrs) {
            scope.Type = attrs.Type;
        }
      };
```

模板
```
 <div ng-switch on="item.Type">
   <div ng-switch-when="1"><ng-include src="'views/1.html'"></ng-include></div>
   <div ng-switch-when="2"><ng-include src="'views/2.html'"></ng-include></div>
   <div ng-switch-when="3"><ng-include src="'views/3.html'"></ng-include></div>
 </div>
```

