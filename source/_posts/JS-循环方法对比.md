---
title: JS📝 循环方法对比
date: 2020-03-28 14:44:54
categories:
- 前端
tags: 
- JavaScript
---

循环方法包括：`for`，`for...in`,  `for...of`，`forEach`。

- `for...in` 是用于遍历一个对象中所有**可枚举**的**属性或索引**，包括继承的属性，可以被用于数组，字符串和普通对象，不可用于 `Map` 和 `Set` 对象（因为这两个对象是可迭代对象），数组不推荐使用这个，速度会下降 10-100 倍；如果不想访问继承的属性，可改用 `Object.hasOwnProperty()`。
- `for...of` 被用于遍历**可迭代对象的值**而不是属性/索引，实际上是在遍历具有 `Symbol.iterator` 属性的成员。可以被用于数组，字符串，`Map` 和 `Set` 对象，不可用于普通对象。
- `forEach()` 则是 `Array` 原型中的方法，允许我们迭代数组的元素，可同时获得 value 和 index，只可用于数组。`forEach` 适用于循环次数未知，或者计算循环次数比较麻烦情况，不可通过 `continue/break` 来控制循环。`forEach` 的返回值总是 `undefined`。且不会对未初始化的值进行任何操作（稀疏数组情况）。



📖：[What is the difference between JavaScript's for...in, for...of and forEach?](https://www.30secondsofcode.org/blog/s/javascript-for-in-for-of-foreach/)