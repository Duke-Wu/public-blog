---
title: Angular ngClass 和 ngStyle 的基本使用
date: 2019/1/1 0:0:0
updated: 2019/7/4 11:43:25
comments: true
tags:
- angular
categories:
- tool kits
---


## Angular ngClass 和 ngStyle 的基本使用

#### 1. ngStyle

基本用法：

```html
<div [ngStyle]="{'background-color':'green'}"></<div>
```

判断添加:

```html
<div [ngStyle]="{'background-color':username === 'zxc' ? 'green' : 'red' }"></<div>
```



#### 2. ngClass

第一个参数为css类名称，第二个参数为boolean值，如果为true就添加第一个参数的css类。

基本用法：

```typescript
[ngClass]="{'text-success':true}"
```

判断：

```typescript
[ngClass]="{'text-success':username == 'zxc'}"
[ngClass]="{'text-success':index == 0}"
```





#### 3. 例子，循环显示的第一行添加text-danger样式，文字变红色

```html
const arr = [1, 3, 4, 5, 6]
<ul>
    <li *ngFor="let item of arr, let i = index">
        <span [ngClass]="{'text-danger': i==0}">{{item}}</span>
    </li>
</ul>
```



