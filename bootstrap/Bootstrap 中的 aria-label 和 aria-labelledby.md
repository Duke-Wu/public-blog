## aira-label

正常情况下，form 表单的 input 组件都有对应的 label 。

```html
<form role="form">
  <div class="form-group">
    <label for="name">名称</label>
    <input type="text" class="form-control" id="name" placeholder="请输入名称">
  </div>
</form>
```

当 input 组件获取到焦点时，屏幕阅读器会读出相应的 label 里的文本。

但是如果我们没有给输入框设置 label 时，当其获得焦点时，屏幕阅读器会读出 aria-label 属性的值，aria-label 不会在视觉上呈现效果。

```html
<form role="form">
  <div class="form-group">
    <input type="text" class="form-control" id="name" placeholder="请输入名称" aria-label="名称">
  </div>
</form>
```

**经测试，aria-label 只有加在可被 tab 到的元素上，读屏才会读出其中的内容。**



## aria-labelledby

如果想要屏幕阅读器读的标签文本在其他元素中存在时，可以将其值为该元素的 id。设置 aria-labelledby 的值为某个元素的 id 。那么屏幕阅读器就可以读取它的值。

```html
<body>
  <div class="dropdown">
    <button type="button" class="btn dropdown-toggle" id="dropdownMenu1" data-toggle="dropdown">
    选择你的职业
      <span class="caret"></span>
    </button>
    <ul class="dropdown-menu" role="menu" aria-labelledby="dropdownMenu1">
      <li role="presentation">
        <a role="menuitem" tabindex="-1" href="#">教师</a>
      </li>
      <li role="presentation">
        <a role="menuitem" tabindex="-1" href="#">医生</a>
      </li>
      <li role="presentation">
        <a role="menuitem" tabindex="-1" href="#">学生</a>
      </li>
    </ul>
  </div>
</body>
```

当 ul 获取到焦点时，屏幕阅读器会读：“选择你的职业”。

**如果一个元素同时有 aria-labelledby 和 aria-label ，读屏软件会优先读出 aria-labelledby 的内容。**



## 有关aria-hidden和role

Accessible Rich Internet Applications **(ARIA)** 规定了能够让 Web 内容和 Web 应用（特别是那些由 Ajax 和 JavaScript 开发的）对于残障人士更易使用的各种机制。例如，ARIA 提供了易用的导航地标、JavaScript 组件、表单提示以及错误信息、实时内容更新等。

ARIA 是一组特殊的易用性属性，可以添加到任意标签上，尤其适用于 HTML。role 属性定义了对象的通用类型（例如文章、警告，或幻灯片）。额外的 ARIA 属性提供了其他有用的特性，例如表单的描述或进度条的当前值。

ARIA 在大多数流行的浏览器和屏幕阅读器中得到了实现。尽管如此，实现方式有所不同，而且旧的技术对其支持不好（或者不支持）。使用可以优雅降级的“安全的” ARIA，或者要求用户升级使用新的技术。

 引用https://developer.mozilla.org/zh-CN/docs/Web/Accessibility/ARIA



## 总结

这两个属性是为特殊网页阅读器设置的属性，在一些特殊设备上，当浏览到这样的内容设备会将内容读出来。是为了一些有视力障碍的人能够同样”浏览”网页而准备的。

---------------------
作者：Yu丶 
来源：CSDN 
原文：https://blog.csdn.net/csdn_yudong/article/details/72627937 
版权声明：本文为博主原创文章，转载请附上博文链接！