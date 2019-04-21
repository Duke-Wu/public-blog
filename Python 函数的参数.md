---
title: Python 函数的参数
date: 2019/3/28 17:43:25
---

# Python 函数的参数

## [Function Calling Behavior](https://www.python.org/dev/peps/pep-3102/#id5)

The previous section describes the difference between the old behavior and the new. However, it is also useful to have a description of the new behavior that stands by itself, without reference to the previous model. So this next section will attempt to provide such a description.

When a function is called, the input arguments are assigned to formal parameters as follows:

- For each formal parameter, there is a slot which will be used to contain the value of the argument assigned to that parameter.
- Slots which have had values assigned to them are marked as 'filled'. Slots which have no value assigned to them yet are considered 'empty'.
- Initially, all slots are marked as empty.
- Positional arguments are assigned first, followed by keyword arguments.
- For each positional argument:
  - Attempt to bind the argument to the first unfilled parameter slot. If the slot is not a vararg slot, then mark the slot as 'filled'.
  - If the next unfilled slot is a vararg slot, and it does not have a name, then it is an error.
  - Otherwise, if the next unfilled slot is a vararg slot then all remaining non-keyword arguments are placed into the vararg slot.
- For each keyword argument:
  - If there is a parameter with the same name as the keyword, then the argument value is assigned to that parameter slot. However, if the parameter slot is already filled, then that is an error.
  - Otherwise, if there is a 'keyword dictionary' argument, the argument is added to the dictionary using the keyword name as the dictionary key, unless there is already an entry with that key, in which case it is an error.
  - Otherwise, if there is no keyword dictionary, and no matching named parameter, then it is an error.
- Finally:
  - If the vararg slot is not yet filled, assign an empty tuple as its value.
  - For each remaining empty slot: if there is a default value for that slot, then fill the slot with the default value. If there is no default value, then it is an error.

In accordance with the current Python implementation, any errors encountered will be signaled by raising `TypeError`. (If you want something different, that's a subject for a different PEP.)



## 参考

[PEP-3102文档](https://www.python.org/dev/peps/pep-3102/)
[廖雪峰-Python教程-函数的参数](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431752945034eb82ac80a3e64b9bb4929b16eeed1eb9000)