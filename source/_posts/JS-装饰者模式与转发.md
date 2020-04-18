---
title: JS📝 装饰者模式与转发
date: 2020-03-28 14:44:54
categories:
- 前端
tags: 
- JavaScript
---

### 装饰者模式 Decorators

🌰 缓存装饰者

假设我们有一个 CPU 重负载的函数 `slow(x)`，但它的结果是稳定的。换句话说，对于相同的 `x`，它总是返回相同的结果。

如果经常调用该函数，我们可能希望将结果缓存（记住）下来，以避免在重新计算上花费额外的时间。

```js
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // 可怕的 CPU 过载任务
    return x * this.someMethod(); // (*)
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func(x); // (**)
    cache.set(x, result);
    return result;
  };
}

alert( worker.slow(1) ); // 原始方法有效

worker.slow = cachingDecorator(worker.slow); // 现在对其进行缓存

alert( worker.slow(2) ); // 出错
```

这里的 `cachingDecorator` 是一个装饰者（decorator）：接收另一个函数并改变它的行为。其思想是，我们可以为任何函数调用 `cachingDecorator`，它将返回缓存包装器，即内部返回的函数是一个包装器（wrapper）。

这个包装器并不改变传入的 `func` 的操作，只是在它的行为之上添加了缓存功能。

以上方法在调用是会报错，简化上面的程序进行分析：

```js
function cachingDecorator(func) {
  //...
  return function(x) {
    //...
    console.log(this);// (1)
    let result = func(x); // (2)
    //...
    return result;
  };
}
worker.slow = cachingDecorator(worker.slow); 

alert( worker.slow(2) ); 
```

(1) :  为 `worker`，这是因为这个 function 赋值给了 `worker.slow`，在通过 `worter.slow()` 调用时，`this` 指向 `worker`；

(2)：等于` let result = worker.slow(x);`
 但是由于这里进行了赋值操作，将中间的引用类型 `(worker,'slow',false)` 抛弃了，所以丢失了原来的 this 值，故此时的 `this` 指向 `window`

修正：将 `let result = func(x); ` 修改为 `let result = func.call(this,x)`；即可正常工作。

**装饰者的问题**

如果被装饰的函数（ `slow` ）具有任何属性，而装饰后的函数则将不再具有这些属性，即 `cachingDecorator(slow)` 则是一个没有这些属性的包装器。

这个问题可以通过 `Proxy` 对象来包装函数，通过 `Proxy` 包装的装饰者可保留对函数属性的访问权限。



### 呼叫转移

将所有参数连同上下文一起传递给另一个函数被称为“呼叫转移（call forwarding）。通常使用 `apply` 来完成。

```javascript
let wrapper = function() {
  return original.apply(this, arguments);
};
```

### 方法借用

对于函数的 arguments 来说，它既是可迭代对象又是类数组，但是由于不是真正的数组，所以不能直接调用数组方法，例如： `arguments.join()` 。此时可以使用方法借用来解决。

`[].join.call(arguments)`，即从常规的数组中借用 `join` 方法，并使用 `[].join.call` 在 `arguments` 的上下文中运行它。

解释：`arr.join()` 方法会利用 `this` 将 `arr` 拼接成字符串，通过 `this[0]` 然后拼接逗号的方式依次将整个数组拼接完成。所以改变 `this = arguments` 可以将 `arguments` 拼接起来。





