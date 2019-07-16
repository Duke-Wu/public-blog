## Django REST Framework 基本架构

Django REST Framework 
框架主要包括四个主要概念

## URLs

响应请求的url路由

## Request & Responses

REST framework 包括 Request（继承自HttpRequest） 和 Response(`TemplateResponse`)

## Model Serializers

将model 实体转换为Python的dictionaires, 然后 渲染成多个API适用的格式，例如JSON或XML。

## Class-based Views

基于class的view可以用于复用代码，提供API到浏览器的显示。



Django-rest-framework 原理解析：

https://www.cnblogs.com/eric_yi/p/8422373.html

[django-rest-framework 官方文档记录](https://www.jianshu.com/p/972a0a67a87e)

https://www.cnblogs.com/zivwong/category/1064692.html