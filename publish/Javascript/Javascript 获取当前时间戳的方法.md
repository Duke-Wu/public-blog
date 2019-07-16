---
title: javascript 获取当前时间戳的方法
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- javascript
categories:
- tool kits
---

# javascript 获取当前时间戳的方法

## JavaScript 获取当前时间戳：
### 第一种方法：(这种方法只精确到秒)

```javascript
var timestamp = Date.parse(new Date());
```
结果：1280977330000

### 第二种方法：

```javascript
var timestamp = (new Date()).valueOf();
```
结果：1280977330748

### 第三种方法：

```javascript
var timestamp=new Date().getTime()；
```

结果：1280977330748

### 备注


>第一种：获取的时间戳是把毫秒改成000显示，因为这种方式只精确到秒
第二种和第三种是获取了当前毫秒的时间戳。



## js时间戳怎么转成日期格式（原理有待研究）

> 运行环境：macOS Mojave 10.14, Google Chrome 版本 71.0.3578.98（正式版本） （64 位）

#### 第一种

```javascript
function getLocalTime1(nS) {     
   return new Date(parseInt(nS) * 1000).toLocaleString().replace(/:\d{1,2}$/,' ');     
}
alert(getLocalTime1(1547004388));
// 结果是 "2019/1/9 上午11:26 "
```

#### 第二种

```javascript
function getLocalTime2(nS) {     
	return new Date(parseInt(nS) * 1000).toLocaleString()
}
alert(getLocalTime2(1547004388));
// 结果是 2019/1/9 上午11:26:28"
```

#### 第三种

```javascript
// 格式为：2010-10-20 10:00:00
function getLocalTime3(nS) {
    return new Date(parseInt(nS) * 1000).toLocaleString().replace(/年|月/g, "-").replace(/日/g, " ");      
}
alert(getLocalTime3(1547004388));
// 结果是  "2019/1/9 上午11:26:28"
```



## 其它问题

```javascript
var a=(new Date()).toLocaleDateString()//获取当前日期
a =a.replace(/\//g,'-');//替换2017/05/03 为    2017-05-03
var nowdate= (new Date(a))/1000;//把当前日期变成时间戳
var wdate=(new Date(v.wdate))/1000;//把数据库日期变成时间
```



## 参考

[[javascript\] JS获取当前时间戳的方法](https://www.cnblogs.com/lipcblog/p/6725347.html)