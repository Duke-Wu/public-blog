---
title: Javascript 判断各种数据类型
date: 2019/7/31 10:24:0
updated: 2019/7/31 10:24:0
comments: true
tags:
- Javascript
categories:
- tool kits
---


### 最新的 ECMAScript 标准定义了 7 种数据类型:
> 详情：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures

6 种原始类型:
- Boolean
- Null
- Undefined
- Number
- String
- Symbol (ECMAScript 6 新定义)

和
- Object



### typeof 操作符返回一个字符串，表示未经计算的操作数的类型。
> 详情：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof

示例：

```js
// Numbers
typeof 37 === 'number';
typeof 3.14 === 'number';
typeof Math.LN2 === 'number';
typeof Infinity === 'number';
typeof NaN === 'number'; // 尽管NaN是"Not-A-Number"的缩写
typeof Number(1) === 'number'; // 但不要使用这种形式!

// Strings
typeof "" === 'string';
typeof "bla" === 'string';
typeof (typeof 1) === 'string'; // typeof总是返回一个字符串
typeof String("abc") === 'string'; // 但不要使用这种形式!

// Booleans
typeof true === 'boolean';
typeof false === 'boolean';
typeof Boolean(true) === 'boolean'; // 但不要使用这种形式!

// Symbols
typeof Symbol() === 'symbol';
typeof Symbol('foo') === 'symbol';
typeof Symbol.iterator === 'symbol';

// Undefined
typeof undefined === 'undefined';
typeof declaredButUndefinedVariable === 'undefined';
typeof undeclaredVariable === 'undefined'; 

// Objects
typeof {a:1} === 'object';

// 使用Array.isArray 或者 Object.prototype.toString.call
// 区分数组,普通对象
typeof [1, 2, 4] === 'object';

typeof new Date() === 'object';

// 下面的容易令人迷惑，不要使用！
typeof new Boolean(true) === 'object';
typeof new Number(1) === 'object';
typeof new String("abc") === 'object';

// 函数
typeof function(){} === 'function';
typeof class C{} === 'function'
typeof Math.sin === 'function';
typeof new Function() === 'function';
```



#### 使用Array.isArray 或者 Object.prototype.toString.call
> 详情：https://www.cnblogs.com/a546558309/p/3608194.html

示例：

```js
var   gettype=Object.prototype.toString
gettype.call('aaaa')        输出      [object String]
gettype.call(2222)          输出      [object Number]
gettype.call(true)          输出      [object Boolean]
gettype.call(undefined)     输出      [object Undefined]
gettype.call(null)          输出      [object Null]
gettype.call({})            输出      [object Object]
gettype.call([])            输出      [object Array]
gettype.call(function(){})  输出      [object Function]
```


#### constructor也能判断数据类型：

如:
```
'abc'.constructor==String    
[].constructor==Array
var obj= new Object()   obj.constructor==Object
```

其实js 里面还有好多类型判断  [object HTMLDivElement] div 对象，[object HTMLBodyElement]  body 对象，[object Document](IE) 或者  [object HTMLDocument]（firefox,google） ......各种dom节点的判断，这些东西在我们写插件的时候都会用到。

可以封装的方法如下：
```
var gettype=Object.prototype.toString
var utility={
    isObj:function(o){
        return    gettype.call(o)=="[object Object]";
    },

    isArray:function(o){
        return    gettype.call(o)=="[object Array]";
    },

    isNULL:function(o){
        return    gettype.call(o)=="[object Null]";
    },

    isDocument:function(){
        return    gettype.call(o)=="[object Document]"|| [object HTMLDocument];
    }

    ........
}
```

这个获取类型的方法有个简单的写法：

  

```js
var Type = (function() {
  var type = {};
  var typeArr = ['String', 'Object', 'Number', 'Array','Undefined', 'Function', 'Null', 'Symbol'];
  for (var i = 0; i < typeArr.length; i++) {
    (function(name) {
      type['Is' + name] = function(obj) {
        return Object.prototype.toString.call(obj) == '[object ' + name + ']';
      }
    })(typeArr[i]);
  }
  return type;
})();
```

调用方法：
```js
Type.IsFunction(function() {})
Type.IsObject({})
......
```

​    