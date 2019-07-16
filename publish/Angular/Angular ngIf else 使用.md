---
title: Angular *ngIf else 使用
date: 2019/3/24 11:36:25
updated: 2019/7/4 11:40:25
comments: true
tags:
- angular
categories:
- tool kits
---


# Angular *ngIf else 使用

> 在Angular 4 and 5中使用 else:


```html
<div *ngIf="isValid;else other_content">
    content here ...
</div>
<ng-template #other_content>other content here...</ng-template>
```
<!--more-->
> 也可以使用以下方式使用else:


```html
<div *ngIf="isValid;then content else other_content">here is ignored</div>
<ng-template #content>content here...</ng-template>
<ng-template #other_content>other content here...</ng-template>
```

> 或者下边的方式 :


```html
<div *ngIf="isValid;then content"></div>
<ng-template #content>content here...</ng-template>
```

