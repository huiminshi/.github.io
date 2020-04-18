---
layout: 
title: JS📝 变量与常量
date: 2020-03-27 19:38:17
categories:
- 前端
tags: 
- JavaScript
---

### var

- `var` 只有函数作用域和全局作用域，没有块级作用域，因为在旧版的 JS 中块时没有词法环境的；
- `var`声明会发生变量提升（hoisting），即提升到脚本/函数顶部，但是 `var` 的赋值操作不会被提升，依旧保留在原来的位置；
-  `var` 声明的全局函数和变量会成为全局对象的属性。

### let

- 作用域是块级作用域（之前只有函数作用域和全局作用域）

- 不存在变量声明提前 （在let之前使用，会报错 is not defined）

- 不可以重复定义 （var可以，不会报错，但是let 会说has already been declared)

- 存在暂时性死区

  在一个块级作用域中，变量唯一存在，一旦声明了一个，就属于这个块级作用域，不受外部的影响

  本质就是：只要已进入当前作用域，所要用的变量就已经存在了，但是不可以获取，只要等到声明变量那行开始才可以获取使用

  意义：标准化代码，将所有的变量声明放在最前面

  ```js
  var a=1;
  if(1){
    console.log(a);
    let a=2;
  }
  //cannot access 'a' before initialization
  ```

### const

- 一般用来声明常量，不允许修改
- 只读属性，声明同时就要赋值
- 和let一样，都是块级作用域，存在暂时性死区，没有变量声明提前，不允许重复定义

### 存储位置

var 定义的变量存储在作用中全局变量（Global）中（也就是window）。

let定义的变量存储在于global同级的这个script的这个域中。

### IIFE 立即执行函数

IIFE 是为了解决 `var` 没有块级作用域的问题提出的办法，IIFE 的函数表达式会被括号 `(function {...})` 包裹起来。这样的书写方式是因为在 JS 中函数声明必须要有函数名，同时 JS 不允许立即调用函数声明。

因此利用圆括号把告函数表达式包起来，以告诉 JavaScript，这个函数是在另一个表达式的上下文中创建的，它是一个函数表达式：故它不需要函数名，可以立即调用。

🌰 多种 IIFE 的书写方式：

```js
(function() {
  alert("Parentheses around the function");
})();

(function() {
  alert("Parentheses around the whole thing");
}());

!function() {
  alert("Bitwise NOT operator starts the expression");
}();

+function() {
  alert("Unary plus starts the expression");
}();
```
