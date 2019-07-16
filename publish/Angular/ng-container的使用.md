---
title: Angular ng-container 的使用
date: 2019/1/1 0:0:0
updated: 2019/7/4 11:40:25
comments: true
tags:
- angular
categories:
- tool kits
---

> ng-container既不是一个Component，也不是一个Directive，只是单纯的一个特殊tag。这点类似于template，Angular复用了HTML5规范中template 的tag的语义，不过并没有真正利用其实现，因此在审查元素中是永远也找不到一个template元素的。不过，由于ng-container并不是HTML5中的，为了保持区分度，采用了ng-作为前缀。所以现在我们可以知道，ng-container是Angular所定义的一个特殊tag。

## Angular ng-container 的使用

#### 1、基本的`ng`代码

```html
<div>
  <ng-container>
    <p>This is paragraph 1.</p>
    <p>This is paragraph 2.</p>
  </ng-container>
</div>
```

#### 2、运行后的结果

```html
<div>
  <p>This is paragraph 1.</p>
  <p>This is paragraph 2.</p>
</div>
```

#### 3、常见的使用场景

```html
<ul class="book-list">
  <ng-container *ngFor="let book of books">
    <li class="book-item">{{ book.title }}</li>
    <li class="book-item">{{ book.author }}</li>
    <li class="book-item">{{ book.price }}</li>
  </ng-container>
</ul>
```
