## TypeScript Object属性获取

请使用这种写法:

```typescript
object["p"]
```

不要使用这种写法(会报错找不到属性):

```typescript
object.p
```



## JavaScript条件判断式之等于=

**不是false, 0, undefined, NaN, "" or null，js都认为是true;**

#### 1、当等于号两边的类型不同时

单个判断真假，然后再判断两边的真假值是否相同

eg:

```javascript
 var a = 0;//a为零
 var b="";//b为空值
 var c = " ";//c为空格
 if( a == b){
     //此时，a和b 的类型不一致，a为0，则为false,b为空值，在为false,false==false,条件满足，进入
 }
```

#### 2、当等于号两边类型相同时

直接对比是否完全一样

eg:

```javascript
var a = 1;
var b = 2;
if(a == b){
//因为a和b的类型都是数值型，类型相同，直接比较，1 ！= 2
```

   }

#### 3、js还自带一种判断   ===

判断不相同类型的两个值可以用===，三等符可以判断类型

eg:

```javascript
("" === 0) 就是false.
```





## JavaScript中数字与字符串相互转化

#### 一.数字转换成字符串

1. Number类定义的`toString()`方法：这个方法可以接收表示转换基数（radix，范围在2~36之间）的可选参数，如果不指定此参数，转换规则将是基于十进制。例如：![Number_ToString](http://img-note.wuqianlin.cn/img-md/2019-01-15-064423.png)

   

2. Number类定义的`toFixed()`方法：这个方法可以指定小数点后的位数。例如：![](http://img-note.wuqianlin.cn/img-md/2019-01-15-064617.png)

   

3. Number类定义的`toExponential()`方法：这个方法使用指数记数法将数字转换为指数形式的字符串，其中小数点前只有一位，小数点后的位数则由参数指定。例如：![屏幕快照 2019-01-15 下午2.51.08](http://img-note.wuqianlin.cn/img-md/2019-01-15-065146.png)

   

4. Number类定义的toPrecision()方法：这个方法根据指定的有效数字位数将数字转换成字符串。如果有效数字的位数少于数字整数部分的位数，则转换成指数形式。例如：![屏幕快照 2019-01-15 下午2.54.03](http://img-note.wuqianlin.cn/img-md/2019-01-15-065420.png)

> 注意：以上这些方法在调用后n的值均没有发生变化，只是返回了相应的字符串结果，并且2、3、4方法在返回结果时有四舍五入。



#### 二.字符串转换为数字

1. 通过Number()转换函数传入一个字符串，它会试图将其转换为一个整数或浮点数直接量，这个方法只能基于十进制进行转换，并且字符串中不能出现非数字的字符，否则将返回NaN。



2. parseInt()函数：它是全局函数，不从属于任何类的方法，且只解析整数。如果字符串前缀是"0x"或者"0X"，则parseInt()将其解释为十六进制数。它解析时会跳过任意数量的前导空格，尽可能解析更多数值字符，并忽略后面的内容，如果第一个非空格字符是非数字字符，则返回NaN。例如：![image-20190115150221880](http://img-note.wuqianlin.cn/img-md/2019-01-15-070222.png)parseInt()还可以接收第二个可选参数，这个参数指定数字转换的基数，合法的取值范围是2~36，例如：

   ![image-20190115150443149](http://img-note.wuqianlin.cn/img-md/2019-01-15-070443.png)



3. parseFloat()函数：它也是全局函数，不从属于任何类的方法，它可以解析整数和浮点数。它不能识别十六进制前缀"0x"或"0X"。它解析时也会跳过任意数量的前导空格，尽可能解析更多数值字符，并忽略后面的内容，如果第一个非空格字符是非数字字符，则返回NaN。例如：![image-20190115150658042](http://img-note.wuqianlin.cn/img-md/2019-01-15-070658.png)



## js 判断数据是否为空

```javascript
//    var a = "";
//    var a = " ";
//    var a = null;
//    var a = undefined;
//    var a = [];
//    var a = {};
//    var a = NaN;
    
    if(a === undefined) { // 只能用 === 运算来测试某个值是否是未定义的
        console.log("为undefined");
    }
    
    if(a == null) { // 等同于 a === undefined || a === null
        console.log("为null");
    }

    
    // String    
    if(a == "" || a == null || a == undefined){ // "",null,undefined
        console.log("为空");
    }
    if(!a){ // "",null,undefined,NaN
        console.log("为空"); 
    }
    if(!$.trim(a)){ // "",null,undefined
        console.log("为空");
    }

    // Array
    if(a.length == 0){ // "",[]
        console.log("为空");
    }
    if(!a.length){ // "",[]
        console.log("为空");
    }

    // Object {}
    if($.isEmptyObject(a)){ // 普通对象使用 for...in 判断，有 key 即为 false
        console.log("为空");
    }
```