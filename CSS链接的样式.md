# CSS链接的样式

a:link, a:visited, a:hover, a:active

1. a:link（未被访问）
2. a:hover（鼠标悬停）
3. a:visited（访问过：真正到达那个页面）
4. a:active（鼠标点击与释放之间、对无href属性的a对象无作用）

这几个元素，定义CSS时候的顺序不同，也会直接导致链接显示的效果不同，本人总结了一下，“特殊性”大的后定义就能实现想要的效果

特殊性由一般到特殊排序：link--visited--hover--active

如下就能实现所要的效果：

```css
a:link{color:blue}
a:visited{color:red}
a:hover{color:yellow}
a:active{color:white}
```



如果定义成这样：

```css
a:hover{color:yellow}
a:link{color:blue}
a:visited{color:red}
a:active{color:white}
```

就不能看到hover的效果了，因为:link是最一般的效果，作用范围大于hover，所以就把前面那句覆盖了。