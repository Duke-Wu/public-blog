## [Finding matches between multiple JavaScript Arrays](https://stackoverflow.com/questions/11076067/finding-matches-between-multiple-javascript-arrays)

**Question:**

I have multiple arrays with string values and I want to compare them and only keep the matching results that are identical between **ALL** of them.

Given this example code:

```
var arr1 = ['apple', 'orange', 'banana', 'pear', 'fish', 'pancake', 'taco', 'pizza'];
var arr2 = ['taco', 'fish', 'apple', 'pizza'];
var arr3 = ['banana', 'pizza', 'fish', 'apple'];
```

I would like to to produce the following array that contains matches from all given arrays:

```
['apple', 'fish', 'pizza']
```

I know I can combine all the arrays with `var newArr = arr1.concat(arr2, arr3);` but that just give me an array with everything, plus the duplicates. Can this be done easily without needing the overhead of libraries such as underscore.js?

(Great, and *now* i'm hungry too!)

**EDIT** I suppose I should mention that there could be an unknown amount of arrays, I was just using 3 as an example.



**Answer:**

```
var result = arrays.shift().filter(function(v) {
    return arrays.every(function(a) {
        return a.indexOf(v) !== -1;
    });
});
```

***DEMO:*** http://jsfiddle.net/nWjcp/2/

You could first sort the outer Array to get the shortest Array at the beginning...

```
arrays.sort(function(a, b) {
    return a.length - b.length;
});
```

------

For completeness, here's a solution that deals with duplicates in the Arrays. It uses `.reduce()`instead of `.filter()`...

```
var result = arrays.shift().reduce(function(res, v) {
    if (res.indexOf(v) === -1 && arrays.every(function(a) {
        return a.indexOf(v) !== -1;
    })) res.push(v);
    return res;
}, []);
```

***DEMO:*** http://jsfiddle.net/nWjcp/4/