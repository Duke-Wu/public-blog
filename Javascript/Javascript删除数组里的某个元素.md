## Javascript删除数组里的某个元素

#### 删除数组指定的某个元素

首先可以给js的数组对象定义一个函数，用于查找指定的元素在数组中的位置，即索引，代码为：

```javascript
Array.prototype.indexOf = function(val) {
  for (var i = 0; i < this.length; i++) {
    if (this[i] == val) return i;
  }
  return -1;
};
```

然后使用通过得到这个元素的索引，使用js数组自己固有的函数去删除这个元素：

```javascript
Array.prototype.remove = function(val) {
  var index = this.indexOf(val);
  if (index > -1) {
    this.splice(index, 1);
  }
};
```

这样就构造了这样一个函数，比如我有有一个数组：

```javascript
var emp = ['abs','dsf','sdf','fd']
```

假如我们要删除其中的 'fd' ,就可以使用：

```javascript
emp.remove('fd');
```



#### 删除的数组的某一项

splice(index,len,[item])    注释：该方法会改变原始数组。[·](http://caibaojian.com/js-splice-element.html)

splice有3个参数，它也可以用来**替换/删除/添加**数组内某一个或者几个值

index:数组开始下标        len: 替换/删除的长度       item:替换的值，删除操作的话 item为空

如：arr = ['a','b','c','d']

#### 删除

```javascript
//删除起始下标为1，长度为1的一个值(len设置1，如果为0，则数组不变)
var arr = ['a','b','c','d'];
arr.splice(1,1);
console.log(arr);  
//['a','c','d']; 

//删除起始下标为1，长度为2的一个值(len设置2)
var arr2 = ['a','b','c','d']
arr2.splice(1,2);
console.log(arr2); 
//['a','d']
```

#### 替换

```javascript
/替换起始下标为1，长度为1的一个值为‘ttt’，len设置的1
var arr = ['a','b','c','d'];
arr.splice(1,1,'ttt');
console.log(arr);        
//['a','ttt','c','d'] 

var arr2 = ['a','b','c','d'];
arr2.splice(1,2,'ttt');
console.log(arr2);       
//['a','ttt','d'] 替换起始下标为1，长度为2的两个值为‘ttt’，len设置的1
```

#### 添加 

 len设置为0，item为添加的值

```javascript
var arr = ['a','b','c','d'];
arr.splice(1,0,'ttt');
console.log(arr);        
//['a','ttt','b','c','d'] 表示在下标为1处添加一项'ttt'
```



## Detele

delete删除掉数组中的元素后，会把该下标出的值置为undefined，**数组的长度不会变**

```javascript
var arr = ['a','b','c','d'];
delete arr[1];
arr;  
//["a", undefined × 1, "c", "d"] 中间出现两个逗号，数组长度不变，有一项为undefined
```





---------------------
转载自:
作者：前端开发博客
来源：前端开发博客
原文：http://caibaojian.com/js-splice-element.html
版权声明：本文为博主原创文章，转载请附上博文链接！