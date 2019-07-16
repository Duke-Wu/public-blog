---
title: Typescript 遍历Array的方法：for, forEach, every等
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- Typescript
categories:
- tool kits
---

# Typescript 遍历Array的方法：for, forEach, every等

Typescript的官方文档 Iterators and Geneators (https://www.typescriptlang.org/docs/handbook/iterators-and-generators.html）

#### 方法一， for…of 

angular 2中HTML语法绑定也是要的这种语法。

```typescript
let someArray = [1, "string", false];

for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}
```



#### 方法二，for循环

for循环其实是标准的C风格语法。

```typescript
let someArray = [1, "string", false];

// 注意：someArray项目不要在循环中增减，否则会影响每次循环someArray.length的值。
for (var i = 0; i < someArray.length; i ++) {
    console.log(someArray[i]); // 1, "string", false
}
```



#### 方法三， for…in 

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



#### 方法三，forEach

forEach其实是JavaScript的循环语法，TypeScript作为JavaScript的语法超集，当然默认也是支持的。

```typescript
let list = [4, 5, 6];
list.forEach((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
});
```



#### 方法四，every和some 

every和some也都是JavaScript的循环语法，TypeScript作为JavaScript的语法超集，当然默认也是支持的。因为forEach在iteration中是无法返回的，所以可以使用every和some来取代forEach。

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



> 转载
>
> 作者：honey199396 
> 来源：CSDN 
> 原文：https://blog.csdn.net/honey199396/article/details/80676116?utm_source=copy 
> 版权声明：本文为博主原创文章，转载请附上博文链接！