---

title: TypeScript Array类型
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- Typescript
categories:
- tool kits
---

# TypeScript Array类型


### 1. 基础操作
#### 1.1 数组的声明

```typescript
let array1:Array<number>;
let array2:number[];
```



#### 1.2 数组初始化

```typescript
let array1:Array<number> = new Array<number>();
let array2:number[] = [1，2，3];
```

<!--more-->

#### 1.3 数组检测

```typescript
if (Array.isArray(value)) {
    // 对数组执行某些操作
}
```



#### 1.4 赋值、修改、增加

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]

array[0] = 20;          // 修改
console.log(array)      // [20, 2, 3, 4]

array[4] = 5;           // 赋值
console.log(array)      // [20, 2, 3, 4, 5]

array.push(6);          // push()方法可以接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。
console.log(array)      // [20, 2, 3, 4, 5, 6]

array.unshift(8, 0);    // unshift()与shift()用途相反，在数组前端添加任意个项并返回新数组长度。
console.log(array);     // [8, 0, 20, 2, 3, 4, 5, 6]
```



#### 1.5 删除、插入、替换

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]

let pop = array.pop();  // pop()方法则从数组末尾移除最后一项，减少数组的length值，然后返回移除的项。
console.log(array)      // [1, 2, 3]

array.splice(0, 1);     // 删除元素（index, deleteCount）
console.log(array)      // [2, 3]

array.shift();          // shift()移除数组中的第一项并返回该项，同时将数组长度减1。
console.log(array);     // [3]
```
##### splice( )方法
`splice( )`方法主要用途是向数组中部插入项，但使用这种方法的方式有如下3种：

1. 删除：可以删除任意位置的项，只需指定2个参数：要删除的第一项的位置和要删除的项数。例如，splice(0,2)会删除数组中的前两项。

2. 插入：可以向指定位置插入任意数量的项，只需提供3个参数：起始位置、0(要删除的项数)和要插入的项。如果要插入多个项，可以再传入第四、第五，以至任意多个项。

3. 替换：可以向指定位置插入任意多个项，且同时删除任意数量的项，只需指定3个参数：起始位置、要删除的项数和要插入任意数量的项。插入的项数不必与删除的项数相等。


splice()方法始终都会返回一个数组，该数组中包含从原始数组中删除的项(如果没有删除任何项，则返回一个空数组)。     

```typescript
var color = ['red','green','blue']
var removed = color.splice(0,1);    // 删除第一个元素
console.log(removed)     // ["red"]
removed = color.splice(1,0,"yellow","orange");    // 插入两个元素在位置1后
console.log(color)       // ["green", "yellow", "orange", "blue"]
console.log(removed)     // []
removed = color.splice(1,1,"red","purple");       // 替换位置1元素
console.log(removed)     // ["yellow"]
console.log(color)       // ["green", "red", "purple", "orange", "blue"]
```



#### 1.6 合并、断开数组

`contact( )`先创建当前数组的一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。

`slice( )`方法返回指定起始位置的一个新的数组。`slice( )`方法可以接收一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下，slice()方法返回从该参数指定位置开始到当前数组末尾的所有项；如果有两个参数，该方法返回起始和结束位置之间的项——但不包括结束位置的项。

```typescript
concat(...items: (T | T[])[]): T[];
slice(start?: number, end?: number): T[];
```

```typescript
let array: Array<number> = [1, 2, 3];
let array2: Array<number> = [4, 5, 6];
let arrayValue = 7;
array = array.concat( array2);
console.log(array)          // [1, 2, 3, 4, 5, 6]

array = array.concat(arrayValue);
console.log(array)          // [1, 2, 3, 4, 5, 6, 7]

let newArray = array.slice(2, 4);
console.log(newArray)      // [3, 4]
```



#### 1.7 位置方法

`indexOf( )`和`lastIndexOf( )`都接收两个参数：要查找的项和(可选的)表示查找起点位置的索引；

在比较第一个参数与数组中的每一项时，会使用全等操作符，即要求查找项必须严格相等；

返回查找到的第一个元素所在位置，没找到返回-1。

```typescript

/**
 * 从数组的开头（位置0）开始向后查找。
 **/
indexOf(searchElement: T, fromIndex?: number): number;
/**
 * 从数组的末尾开始向前查找。
 **/
lastIndexOf(searchElement: T, fromIndex?: number): number;
```

```typescript
let array: Array<string> = ["a","b","c","d","c","a"];
let indexC = array.indexOf("c");
console.log(indexC);            // 2
let lastA = array.lastIndexOf("a");
console.log(lastA);             // 5
```



#### 1.8 连接数组元素

```typescript
/**
 * 连接数组
 **/
join(separator?: string): string;
```

```typescript
let array: Array<string> = ["a","b","c","d","c","a"];
let result = array.join();
console.log(result);            // a,b,c,d,c,a

result = array.join("+");
console.log(result);            // a+b+c+d+c+a

result = array.join("");
console.log(result);            // abcdca
```



#### 1.9 重新排序

`sort( )`方法在默认情况下按升序排列数组，为了实现排序，`sort( )`方法会调用每个数组元素的`toString( )`转型方法。然后比较得到的字符串，一确定如何排序。

`sort( )`方法可以接收一个比较函数，以便我们指定那个值位于那个值得前面。

 比较函数接收两个参数，如果第一个参数应该位于第二个之前则返回一个负数；如果两个相等则返回0；
如果第一个参数应该位于第二个之后则返回一个正数。

！！！即使数组中的每一项都是数字，`sort( )`方法也比较的是字符串。

`reverse( )`只是反转数组原来的顺序。

`sort( )`和`reverse( )`方法会修改原来的数组。

```typescript
let array1 = [3, 2, 1, 8, 7, 0, 4];
let array2 = [0, 1, 5, 10, 15];

array1.sort();
console.log(array1);             // [0, 1, 2, 3, 4, 7, 8]
array1.reverse();
console.log(array1)              // [8, 7, 4, 3, 2, 1, 0]

array2.sort();
console2.log(array2);             // [0, 1, 10, 15, 5]
array2.reverse();
console.log(array2);              // [5, 15, 10, 1, 0]

function compare(value1, value2) {
  if (value1 < value2) {
    return -1;
  } else if (value1 > value2) {
    return 1;
  } else {
    return 0
  }
}

var values = [0, 1, 5, 10, 15];
values.sort(compare);
console.log(values);        // [0, 1, 5, 10, 15]

/**
 * 对于数值类型或者其valueOf()方法会返回数值类型的对象类型，可以使用一个更简单的比较函数。
 * 这个函数只要用第二个值减去第一个值即可。
 **/
function compare(value1, value2) {
  return value2 - value1;
}
```



### 2. 遍历与迭代

参考文档：[Typescript的官方文档 Iterators and Geneators](https://www.typescriptlang.org/docs/handbook/iterators-and-generators.html)

#### 2.1 for…of 

angular 2中HTML语法绑定也是要的这种语法。

```typescript
let someArray = [1, "string", false];

for (let entry of someArray) {
    console.log(entry);    // 1, "string", false
}
```



#### 2.2 for循环

for循环其实是标准的C风格语法。

```typescript
let someArray = [1, "string", false];

// 注意：someArray项目不要在循环中增减，否则会影响每次循环someArray.length的值。
for (var i = 0; i < someArray.length; i ++) {
    console.log(someArray[i]); // 1, "string", false
}
```



#### 2.3 for…in 

官方文档上强调了for…in和for…of的区别：

```typescript
let list = [4, 5, 6];

for (let i in list) {
   console.log(i); // "0", "1", "2",
}

for (let i of list) {
   console.log(i); // "4", "5", "6"
}
```



#### 2.4 forEach

forEach其实是JavaScript的循环语法，TypeScript作为JavaScript的语法超集，当然默认也是支持的。

forEach( )方法没有返回值，本质上与使用for循环迭代数组一样。

```typescript
let list = [4, 5, 6];
list.forEach((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
});
```



#### 2.5 every和some 

every和some也都是JavaScript的循环语法，TypeScript作为JavaScript的语法超集，当然默认也是支持的。因为forEach在iteration中是无法返回的，所以可以使用every和some来取代forEach。

every( )：对数组中的每一项运行给定的函数，如果该函数对**每一项**都返回true，则返回true。

some( )：对数组中的每一项运行给定的函数，如果该函数对**任意项**返回true，则返回true。

```typescript
let list = [4, 5, 6];
list.every((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
    return true; // Continues
    // Return false will quit the iteration
});
```



#### 2.6 filter

filter( )：对数组中的每一项运行给定的函数，返回该函数会返回true的项组成的数组。

```typescript
var numbers = [1,2,3,4,5,4,3,2,1];
var filterResult = numbers.filter(function(item, index, array) {
    return (item > 2);
})
console.log(filterResult);
```



#### 2.7 map

map( )：对数组的每一项运行给定的函数，返回每次函数调用的结果组成的数组。

```typescript
var numbers = [1,2,3,4,5,4,3,2,1];
var mapResult = numbers.map(function(item, index, array) {
    return item * 2;
});
alert(mapResult);    // [2, 4, 6, 8, 10, 8, 6, 4, 2]
```



### 3. reduce 和 reduceRight

`reduce( )`和`reduceRight( )`这两个方法都会迭代数组的所有项，然后构建一个最终返回值。其中，`reduce( )`方法从数组的第一项开始，逐个遍历到最后。而`reduceRight( )`则从数组的最后一项开始，向前遍历到第一项。

这两个方法都接收两个参数：一个在每一项上调用的函数和（可选的）作为缩小基础的初始值。传给`reduce()`和`reduceRight()`的函数接收四个参数：前一个值、当前值、项的索引和数组对象。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数是数组的第二项。

```typescript
var values = [1,2,3,4,5];
var sum = values.reduce(function(prev, cur, index, array) {
    return prev + cur
})
console.log(sum);    // 15

// reduceRight() 作用类似，只不过方向相反而已。
var sum = values.reduceRight(function(prev, cur, index, array) {
    return prev + cur
})
console.log(sum);     // 15
```

