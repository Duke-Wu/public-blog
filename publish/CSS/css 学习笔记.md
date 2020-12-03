---
title: css 学习笔记
date: 2019/1/1 0:0:0
updated: 2019/7/4 13:51:00
comments: true
tags:
- css
categories:
- css
---

# css 学习笔记

##### display：block（块级元素 ） 
会独占一行，多个block元素会各自新起一行。默认情况下，block元素宽度自动填满其父级宽度

##### display：inline（内联（行级）元素 ）

不会独占一行，多个相邻的行内元素会排列在同一行里，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化

##### display:inline-block

既具有block的宽度高度特性又具有inline的同行特性



#### 三者细节对比

##### display:block

block元素会独占一行，多个block元素会各自新起一行。默认情况下，block元素宽度自动填满其父元素宽度。

block元素可以设置width,height属性。块级元素即使设置了宽度,仍然是独占一行。

block元素可以设置margin和padding属性。

##### display:inline

inline元素不会独占一行，多个相邻的行内元素会排列在同一行里，直到一行排列不下，才会新换一行，其宽度随元素的内容而变化。

inline元素设置width,height属性无效。

inline元素的margin和padding属性，水平方向的padding-left, padding-right, margin-left, margin-right都产生边距效果；但竖直方向的padding-top, padding-bottom, margin-top, margin-bottom不会产生边距效果。

##### display:inline-block

简单来说就是将对象呈现为inline对象，但是对象的内容作为block对象呈现。之后的内联对象会被排列在同一行内。比如我们可以给一个link（a元素）inline-block属性值，使其既具有block的宽度高度特性又具有inline的同行特性。



#### white-space和word-wrap和word-break 所表示的换行和不换行的区别

## 一、前言

　　使得文本换行有很多方式，

1. <br/>标签元素，能够强制使得所在位置文本换行
2. <p>元素，<div>设定宽度，都可以对文本内容实现自适应换行
3. 对于长单词或者链接，默认不会断开换行，方式2就不能够在其这些文本内部进行换行了，
   这时就需要word-wrap : break-word ;或者[word-break](http://www.divcss5.com/shouce/c_wordbreak.shtml):break-all;实现强制断行

 

## 二、正文

### 1. 强制不换行

```html
div{
    white-space:nowrap;
}
/*
white-space：normal 默认 
pre 换行和其他空白字符都将受到保护
nowrap 强制在同一行内显示所有文本，直到文本结束或者遭遇 br 对象
*/
```

### 2. 控制文本换行

```
div{
   word-break: normal;
　　word-break: break-all;
　　word-break: keep-all;
}
/*
word-break: 
normal ; 依照亚洲语言和非亚洲语言的文本规则，允许在字内换行
break-all : 　该行为与亚洲语言的normal相同。也允许非亚洲语言文本行的任意字内断开。该值适合包含一些非亚洲文本的亚洲文本
keep-all : 　与所有非亚洲语言的normal相同。对于中文，韩文，日文，不允许字断开。适合包含少量亚洲文本的非亚洲文本
*/
```



### 3. 强制单词内或链接内断行

```
div{
　　word-wrap: break-word;
}
/*
word-wrap 属性用来标明是否允许浏览器在长单词和链接内进行断句
normal: 只在允许的断字点换行(浏览器保持默认处理)
break-word:在长单词或URL地址内部进行换行
*/
```

 

##  详细介绍：

**（一）white-space 属性设置如何处理元素内的空白** 

　　**white-space: normal|pre|nowrap|pre-wrap|pre-line|inherit;** 

- **normal**默认。空白会被浏览器忽略。 
- **pre** 空白会被浏览器保留。其行为方式类似 HTML 中的 pre 标签。 
- **nowrap**文本不会换行，文本会在在同一行上继续，直到遇到 br 标签为止。 
- **pre-wrap** 保留空白符序列，但是正常地进行换行。 
- **pre-line** 合并空白符序列，但是保留换行符。 
- **inherit** 规定应该从父元素继承 white-space 属性的值。

 

**（二）word-wrap 属性用来标明是否允许浏览器在单词内进行断句**

　   **word-wrap: normal|break-word;** 

　　word-wrap 属性用来标明是否允许浏览器在单词内进行断句

- **normal**: 只在允许的断字点换行(浏览器保持默认处理) 

- break-word

  :在

  长单词或URL地址内部进行换行，

  ```
  /*内容将在边界内换行。如果需要，单词内部允许断行。*/
  ```


**（三）word-break 属性用来标明怎么样进行单词内的断句**

　　**word-break: normal|break-all|keep-all;**

- **normal**：使用浏览器默认的换行规则。 
- **break-all**:允许在单词内换行 ， 允许任意非CJK(Chinese/Japanese/Korean)文本间的单词断行。
- **keep-all:**只能在半角空格或连字符处换行，
  不允许CJK(Chinese/Japanese/Korean)文本中的单词换行，只能在半角空格或连字符处换行。非CJK文本的行为实际上和normal一致。

 

## 三、结语

https://www.cnblogs.com/yingzi1028/p/6113066.html

[word-break: break-all;、word-break: keep-all; 、word-wrap: break-word;和white-space:nowrap;都有什么作用](https://www.cnblogs.com/yingzi1028/p/6113066.html)
