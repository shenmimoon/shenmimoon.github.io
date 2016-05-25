---
layout: post
title: AngularJS概览
tags: [AngularJS]
---
## AngularJS概览

> AngularJS 是一个 JavaScript 框架。它可通过 `<script>` 标签添加到 HTML 页面。    
> AngularJS 通过 指令 扩展了 HTML，且通过 表达式 绑定数据到 HTML。    

### AngularJS可以干什么

AngularJS 通过 `ng-directives` 扩展了 HTML。
`ng-app` 指令定义一个 AngularJS 应用程序。
`ng-model` 指令把元素值（比如输入域的值）绑定到应用程序。
`ng-bind` 指令把应用程序数据绑定到 HTML 视图。    
    
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<script src="http://apps.bdimg.com/libs/angular.js/1.4.6/angular.min.js"></script>
	</head>
	<body>

	<div ng-app="">
	 	<p>名字 : <input type="text" ng-model="name"></p>
	 	<h1>Hello {{name}}</h1>
	</div>

	</body>
	</html>
    
HTML5 允许扩展的（自制的）属性，以 `data-` 开头。
AngularJS 属性以 `ng-` 开头，但是您可以使用 `data-ng-` 来让网页对 HTML5 有效。    

### AngularJS 表达式    

AngularJS 表达式写在双大括号内：`{{ expression }}`。    
AngularJS 表达式把数据绑定到 HTML，这与 `ng-bind` 指令有异曲同工之妙。    
AngularJS 将在表达式书写的位置"输出"数据。    
AngularJS 表达式 很像 JavaScript 表达式：它们可以包含文字、运算符和变量。    
实例 `{{ 5 + 5 }}` 或 `{{ firstName + " " + lastName }}` 。     

### 思考    
* AngularJS 的优缺点：
	+ 灵活轻量的 controller 层逻辑，解放服务端
	+ js 语法，dom 结构

