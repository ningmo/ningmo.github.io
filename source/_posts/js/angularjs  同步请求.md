---
title: angularjs同步请求
categories:
  - js
tags: [angularjs]
date: 2015-12-15 14:15:32
---


```js
     .service('Syncservice', function () {
        function fetch(url) {
            var request;
            if (window.XMLHttpRequest) {
                request = new XMLHttpRequest();
            } else if (window.ActiveXObject) {
                request = new ActiveXObject("Microsoft.XMLHTTP");
            } else {
                throw new Error("Your browser don't support XMLHttpRequest");
            }
            request.open('GET', url, false);
            request.send(null);

            if (request.status === 200) {
                return request.responseText;
            }
        }
        return {
            fetch: fetch
        }
	});
```

调用

```
var data = Syncservice.fetch('/api/data/'+i); ```


当然 以上同步请求会阻断当前页面 在console中警告
​``` Synchronous XMLHttpRequest on the main thread is deprecated because of its detrimental effects to the end user's experience. For more help, check http://xhr.spec.whatwg.org/ ```
意思就是在js的主进程中调用同步请求影响用户体验，可以使用worker进程进行同步请求 这样会有较好的用户体验 具体使用方法请自行google之

```