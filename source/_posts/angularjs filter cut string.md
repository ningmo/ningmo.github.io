---
date: 2015-11-11T11:40:21
title: angularjs filter cut string

---

```
angular.module('App.controllers.MyCtrl', [])
.controller('MyCtrl', function (my) {})
.filter('cut', function () {
        return function (value, wordwise, max, tail) {
            if (!value) return '';

            max = parseInt(max, 10);
            if (!max) return value;
            if (value.length <= max) return value;

            value = value.substr(0, max);
            if (wordwise) {
                var lastspace = value.lastIndexOf(' ');
                if (lastspace != -1) {
                    value = value.substr(0, lastspace);
                }
            }

            return value + (tail || '...');
        };
    });
```

用法


```
{{ Desc | cut:true:15:'...' }}
```




