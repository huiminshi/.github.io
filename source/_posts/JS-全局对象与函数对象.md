---
title: JS📝 全局对象与函数对象
date: 2020-04-01 14:03:27
categories:
- 前端
tags: 
- JavaScript
---

### 全局对象

浏览器环境下为 `window`，Node.js 环境下为 `global`。目前 JS 中新添加了 `globalThis` 作为全局对象的标准名称，但有些浏览器还不能兼容。

可以使用全局对象来测试对现代语言功能的支持。

### 函数对象

函数本质是一个对象，所以可以进行增/删属性，按引用传递等操作。

**name 属性**

如果函数本身没有函数名，那么会通过上下文命名推测一个函数名，如果实在不知道具体的函数名，那么会返回空；

🌰

```javascript
let sayHi = function() {
  alert("Hi");
};
alert(sayHi.name); // sayHi（有名字！）
```

**length 属性**

可以返回函数的参数长度，但是 rest 参数不会被计算在内；可以利用 length 属性实现多态性，即根据参数的类型或者具体情况下参数的个数来做不同的处理；

**自定义属性**

🌰 自定义 counter 属性来记录函数调用次数

```javascript
function sayHi() {
  alert("Hi");
  sayHi.counter++;
}
sayHi.counter = 0; // initial value

sayHi(); // Hi
sayHi(); // Hi

alert( `Called ${sayHi.counter} times` ); // Called 2 times
```

**函数属性不是一个变量，以上例子中不会再函数内定义一个局部变量 counter，所以函数属性与变量是两个不相关的东西，即它不会对函数的执行产生影响**。

函数属性可以被用来替代闭包，因为通过闭包的方式从某个函数内部传递出来的变量无法被这个函数外部的环境所修改，利用函数属性的话可以解决这一问题。（见闭包笔记）

在很多 JS 库中都充分利用了自定义属性，例如：jQuery 库创建了一个名为 `$` 的函数和 lodash 库中的 `_` 函数，然后为它们添加属性，例如 `_.add`、`_.keyBy` 等，这样一个库就只会有一个全局变量，可以减少对全局空间的污染，降低了命名冲突的可能性。

**命名函数表达式 NFE**

语法：

```js
let sayHi = function func() {...}
```

NFE 依旧是一个函数表达式，为它添加 `func` 不能使它变为函数声明，它仍然是作为赋值表达式中的一部分被创建的。

NFE 特点：

1. 它允许函数在内部引用自己。
2. 它在函数外不可见。

🌰 使用场景

```javascript
let sayHi = function func(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
    //sayHi("Guest");//这样是无法使用，因为外部将 sayHi 设为了 null
    func("Guest");//这样可正常操作 
  }
};

let welcome = sayHi;
sayHi = null; // 外部修改了 sayHi

welcome(); // Hello, Guest（嵌套调用有效）
```

NFE 相对于普通的函数表达式来说，给函数添加了一个可选的名字（如：`func` ），这个名字是对外不可见的，即外部无法修改它。

在不使用 NFE 的情况下，如果想要在函数内部引用自己，则需要调用外部的函数变量名（ `sayHi` ），但这个外部的函数名是可以被外部修改的，那么可能会造成它被修改后无法正常调用的情况；NFE 可以解决这个问题，因为它所添加的函数名对外不可见，只处在这个函数内部的，所以可以解决这个问题。

函数声明没有内部名这样的特性。