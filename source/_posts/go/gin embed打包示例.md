---
title: gin embed打包示例
categories:
  - go
tags: [gin,golang,embed]
date: 2021-05-21 15:35:00
---

```
package main

import (
	"embed"
	"github.com/gin-gonic/gin"
	"html/template"
	"io/fs"
	"net/http"
)

// 下面这一行非常重要！！！！！！
//go:embed templates static
var FS embed.FS

func main() {
	gin.SetMode(gin.ReleaseMode)

	router := gin.Default()
	templ := template.Must(template.New("").ParseFS(FS, "templates/*.html"))
	router.SetHTMLTemplate(templ)

	fe, _ := fs.Sub(FS, "static")
	router.StaticFS("/static", http.FS(fe))

	router.GET("/", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html",gin.H{})
	})
	router.Run(":8080")
}


```




