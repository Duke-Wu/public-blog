---
title: TypeScript 数组Array操作
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- Typescript
categories:
- tool kits
---

## TypeScript 数组Array操作

#### 一、数组的声明

```typescript
let array1:Array<number>;
let array2:number[];
```

#### 二、数组初始化

```typescript
let array1:Array<number> = new Array<number>();
let array2:number[] = [1，2，3];
```

#### 三、数组元素赋值、添加、更改

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]

array[0] = 20;          // 修改
console.log(array)      // [20, 2, 3, 4]

array[4] = 5;           // 赋值
console.log(array)      // [20, 2, 3, 4, 5]

array.push(6);          // 添加
console.log(array)      // [20, 2, 3, 4, 5, 6]

array.unshift(8, 0);    // 在第一个位置依次添加
console.log(array);     // [8, 0, 20, 2, 3, 4, 5, 6]
```

#### 四、删除

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]

let popValue = array.pop();     // 弹出
console.log(array)      // [1, 2, 3]

array.splice(0, 1);     // 删除元素（index, deleteCount）
console.log(array)      // [2, 3]

array.shift();          // 删除第一个元素
console.log(array);     // [3]
```

#### 五、合并、断开数组

```typescript
/**
  * Combines two or more arrays.
  * @param items Additional items to add to the end of array1.
  */
concat(...items: (T | T[])[]): T[];
/**
 * 该方法返回指定起始位置的一个新的数组
 */
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

#### 六、查找数组元素位置

```typescript
/**
  * 返回查找到的第一个元素所在位置
  */
indexOf(searchElement: T, fromIndex?: number): number;
/**
  * 返回反序查找的第一个元素所在位置
  */
lastIndexOf(searchElement: T, fromIndex?: number): number;
```

```typescript
let array: Array<string> = ["a","b","c","d","c","a"];
let indexC = array.indexOf("c");
console.log(indexC);            // 2
let lastA = array.lastIndexOf("a");
console.log(lastA);             // 5
```

#### 七、连接数组元素

```typescript
/**
 * 连接数组
 */
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

#### 八、排序、反序数组

```typescript
let array:Array<number> = [3, 2, 1, 8, 7, 0, 4];
console.log(array);             // [3, 2, 1, 8, 7, 0, 4]

array.sort();
console.log(array);             // [0, 1, 2, 3, 4, 7, 8]

array.reverse();
console.log(array);             // [8, 7, 4, 3, 2, 1, 0]
```


转载自:
作者：honey199396 
来源：CSDN 
原文：https://blog.csdn.net/honey199396/article/details/80750408?utm_source=copy 
版权声明：本文为博主原创文章，转载请附上博文链接！