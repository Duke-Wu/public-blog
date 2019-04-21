<https://www.cnblogs.com/a546558309/p/3608194.html>

了解js的都知道， 有个typeof  用来判断各种数据类型,有两种写法：typeof   xxx   ,typeof(xxx)

​       如下实例：

​       typeof   2      输出   number
​       typeof   null   输出   object

​       typeof   {}    输出   object

​       typeof    []    输出   object

​       typeof   (function(){})   输出  function

​       typeof    undefined         输出  undefined

​       typeof   '222'                 输出    string

​      typeof  true                   输出     boolean

​    这里面包含了js里面的五种数据类型  number   string    boolean   undefined     object和函数类型 function

​     看到这里你肯定会问了：我怎么去区分对象，数组和null呢?

​     接下来我们就用到另外一个利器：Object.prototype.toString.call

​     这是对象的一个原生原型扩展函数,用来更精确的区分数据类型。

​     我们来试试这个玩儿意儿：

​     var   gettype=Object.prototype.toString

​        gettype.call('aaaa')        输出      [object String]

​        gettype.call(2222)         输出      [object Number]

​        gettype.call(true)          输出      [object Boolean]

​        gettype.call(undefined)  输出      [object Undefined]

​        gettype.call(null)                  输出   [object Null]

​         gettype.call({})                   输出   [object Object]

​         gettype.call([])                    输出   [object Array]
​         gettype.call(function(){})     输出   [object Function]

​      看到这里，刚才的问题我们解决了。 

​     constructor也能判断数据类型：

​     如:''.constructor==String    

​           [].constructor==Array

​           var obj= new Object()   obj.constructor==Object

​      其实js 里面还有好多类型判断      [object HTMLDivElement]     div 对象  ,    [object HTMLBodyElement]  body 对象    ,[object Document](IE)或者  [object HTMLDocument]（firefox,google） ......各种dom节点的判断，这些东西在我们写插件的时候都会用到。

​     可以封装的方法如下  ：

​      var   gettype=Object.prototype.toString

​     var    utility={

​          isObj:function(o){

​               return    gettype.call(o)=="[object Object]";

​          },

​          isArray:function(o){

​               return    gettype.call(o)=="[object Array]";

​          },

​          isNULL:function(o){

​               return    gettype.call(o)=="[object Null]";

​          },

​          isDocument:function(){

​                return    gettype.call(o)=="[object Document]"|| [object HTMLDocument];

​          }

​          ........

​    }

​    这个获取类型的方法有个简单的写法：

  

```
`var` `Type = (``function``() {``                ``var` `type = {};``                ``var` `typeArr = [``'String'``, ``'Object'``, ``'Number'``, ``'Array'``,``'Undefined'``, ``'Function'``, ``'Null'``, ``'Symbol'``];``                ``for` `(``var` `i = 0; i < typeArr.length; i++) {``                    ``(``function``(name) {``                        ``type[``'Is'` `+ name] = ``function``(obj) {``                            ``return` `Object.prototype.toString.call(obj) == ``'[object '` `+ name + ``']'``;``                        ``}``                    ``})(typeArr[i]);``                ``}``                ``return` `type;``})();`
```

　　 

```
`调用方法：Type.IsFunction(``function``() {})      Type.IsObject({})。。。。。`
```

​     今天讲的内容不是很多，大家对于当前所讲的东西有啥要补充的可以留个言。。。