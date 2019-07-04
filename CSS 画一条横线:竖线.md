作为优秀的java程序员,扎实(la ji )前端水平是我工作的基础 , 所以今天记录一下怎么用css画一条横线/竖线出来 , 以此为笔记, 将来不需要去翻阅别人的代码

废话不多说  笔记开始

### CSS 代码

```css
/*中间的过度的横线*/
.link-top {
  width: 50%;
  height: 1px;
  border-top: solid #ACC0D8 1px;
}
 
/*画一条再右边的竖线*/
.link-right {
  width: 50px;
  height: 20%;
  border-right: solid #ACC0D8 1px;
}
```

### 整体的HTML代码

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>画一个直线</title>
    <style>
        /*中间的过度的横线*/
        .link-top {
            width: 50%;
            height: 1px;
            border-top: solid #ACC0D8 1px;
        }
 
        /*画一条再右边的竖线*/
        .link-right {
            width: 50px;
            height: 20%;
            border-right: solid #ACC0D8 1px;
        }
 
    </style>
</head>
<body>
<h1>Hellow World</h1>
<div class="link-top"></div>
<p class="link-right">段落1</p>
<div class="link-top"></div>
<p>段落2</p>
</body>
</html>
```



### 补充:

上面的图片很丑 , 后来发现了画竖线的妙法就是直接用 | 这个代替,效果非常好