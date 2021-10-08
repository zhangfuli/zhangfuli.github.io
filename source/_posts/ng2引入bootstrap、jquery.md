---
title: ng2引入bootstrap、jquery
date: 2017-04-16 17:37:41
tags: angularjs
categories: angularjs
---

### ng2笔记(angular-cli)

###### ng2引入bootstrap、jquery
	npm install ng2-bootstrap bootstrap --save
	npm install jquery --save


###### 在angular-cli.json里引入

	"styles": [
        "styles.css",
        "../node_modules/bootstrap/dist/css/bootstrap.min.css"
      ],
     "scripts": [
        "../node_modules/jquery/dist/jquery.min.js",
        "../node_modules/bootstrap/dist/js/bootstrap.min.js"
     ]

