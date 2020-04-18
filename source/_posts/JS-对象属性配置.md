---
title: JS📝 对象属性配置
date: 2020-04-06 19:22:00
categories:
- 前端
tags:
- JavaScript
---

### 属性标志

对象属性除了 `value`，还有以下三个特殊的属性：

- `writable` ：为 `true` 的话表示只可以被改变，否则该对象为只读对象；
- `enumerable` ：为 `true` 表示可枚举；
- `configurable` ：为 `true` 表示该对象属性可被删除和修改。

在创建对象时，默认这些属性都为 `true`。

🌰

```js
let user = {
    name : 'John'
};

//此时 name 属性的配置如下
[object Object] {
  configurable: true,
  enumerable: true,
  value: "John",
  writable: true
}
```

**non-writable**

即 `writable:false`，在严格模式下对指定的只读属性进行写的操作会报错，在非严格模式下不会，但依旧不可执行写操作。

**non-enumerable**

对于拥有内置的 `toString` 方法的对象，是不可枚举的，但是如果手动添加这个方法且没有设置 `enumerable` 为 `false`，则是可枚举的。`non-enumerable` 的属性也不会被 `Object.keys` 等方法获得。

🌰

```javascript
let user = {
  name: "John",
  toString() {
    return this.name;
  }
};

// By default, both our properties are listed:
for (let key in user) alert(key); // name, toString
```

**non-configurable**

当设置某个属性为 `configurable:false`，会增加对 `defineProperty` 的限制：

1. 不能改变 `configurable` 标识符；
2. 不能改变 `enumerable` 标识符；
3. 不能改变 `writable:false` 为 `true`，但是可以将 `writable:true` 变回 `false`；
4. 不能修改访问者属性（accessor property）的 `get/set`，但是如果没有可以分配；

non-configurable 不意味着 non-writable：

`configurable:false` 的目的是为了防止属性标识符被改变和删除，而不是防止属性的值被修改，所以与 non-writable 是不同的。

### 属性描述符

 **`Object.getOwnPropertyDescriptor`**

可以通过 `Object.getOwnPropertyDescriptor` 获取一个属性的所有信息。

```js
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName)
```

 **`Object.defineProperty`** 

可以通过 `Object.defineProperty` 修改标识符

```js
Object.defineProperty(obj, propertyName, descriptor)
```

如果该属性存在，`defineProperty` 会更新它的标志，否则会使用给定的值和标识创建属性，如果在这个情况下没有提供标志，则会假定它为 `false`。

🌰

```javascript
let user = {};

Object.defineProperty(user, "name", {
  value: "John"
});

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/*
{
  "value": "John",
  "writable": false,
  "enumerable": false,
  "configurable": false
}
 */
```

**`Object.defineProperties9`**

这个方法可以一次性设置多个属性和对象的描述符

```javascript
Object.defineProperties(obj, {
  prop1: descriptor1,
  prop2: descriptor2
  // ...
});
//例如
Object.defineProperties(obj, {
    name: {value: 'John', writable: false},
    surname:{value: 'Smith', writable: false},
    //...
})
```

**`Object.getOwnPropertyDescriptors`**

这个方法与 `Object.defineProperties` 结合可以将被克隆对象的的标识符也克隆下来。

同时 `Object.getOwnPropertyDescriptors` 方法不会忽略 symbolic 属性，而 `for...in` 则会忽略。

```js
let clone = Object.defineProperties({}, Object.getOwnPropertyDescriptors(obj));
//普通的克隆
for(let key in user){
    clone[key] = user[key];
}
```

### 全局的密封对象

由于属性描述符是作用在单个属性上，以下是一些限制访问整个对象的方法：

- `Object.preventExtensions(obj)`：禁止向对象添加新属性；
- `Object.seal(obj)`：禁止添加/删除/修改属性。等同于为所有的属性都设置 `configurable:false`；
- `Object.freeze(obj)`：禁止添加/删除/更改属性，等同于为所有属性设置 `configurable:false,writable:false`；
- `Object.isExtensible(obj)`：如果添加属性被禁止，则返回 `false`，否则为 `true`；
- `Object.isSealed(obj)`：如果添加/删除属性被禁止，且所有的属性都具有 `configurable:false` 则返回 `true`；
- `Object.isFrozen(obj)`：如果添加/删除/更改属性被禁止，且所有属性都是 `configurable:false,writable:false`，则返回 `true`。

### 属性的 getters 和 setters

两种属性类型：

- 数据属性（data properties）：具有 `value`
- 访问器（accessor properties）：本质上是函数，用于获取和设置值，看起来像常规属性，没有 `value`。

访问器属性由 getter 和 setter 方法表示，在对象字面量中，用 `get` 和 `set` 表示：

```javascript
let obj = {
  get propName() {
    // 当读取 obj.propName 时，getter 起作用
  },
  set propName(value) {
    // 当执行 obj.propName = value 操作时，setter 起作用
  }
};
```

🌰

```js
let user = {
  name: 'John',
  surname: 'Smith',
  
  get fullName() {
    return `${this.name} ${this.surname}`;
  },
  set fullName(value) {
    [this.name, this.surname] = value.split(' ');
  }
}

user.fullName = 'Alice Cooper';
console.log(user.name);//Alice
console.log(user.surname);//Cooper
console.log(user.fullName);//Alice Cooper
```

**访问器属性描述符**

访问器属性的描述符和数据属性不同，没有 `value` 和 `writable`，但是具有 `get` 和 `set` 函数。

访问器描述符可能有：

- `get`：一个没有参数的函数，在读取属性时工作，
- `set` ：带有一个参数的函数，当属性被设置时调用，
- `enumerable`：与数据属性的相同，
- `configurable`：与数据属性的相同。

🌰 使用 `defineProperty` 创建 `fullName` 访问器

```js
Object.defineProperty(user, 'fullName', {
    get() {
        return `${this,name} ${this.surname}`;
    },
    set(value) {
        [this.name, this.surname] = value.split(' ');
    }
});
```

在原型继承中修改父元素的访问器属性时，可以进行写操作，因为访问器属性实际上是函数的形式，所以当进行写操作时与调用函数一样。

🌰 以下例子中如果 `fullName` 是数据属性，则不能修改父元素的这个属性

```javascript
let user = {
  name: "John",
  surname: "Smith",

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  },

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

let admin = {
  __proto__: user,
  isAdmin: true
};


console.log(admin.fullName); // John Smith

admin.fullName = "Alice Cooper"; 
console.log(user.fullName);// John Smith
console.log(admin.fullName);// Alice Cooper
```

🌰

```javascript
// animal has methods
let animal = {
  walk() {
    if (!this.isSleeping) {
      alert(`I walk`);
    }
  },
  sleep() {
    this.isSleeping = true;
  }
};

let rabbit = {
  name: "White Rabbit",
  __proto__: animal
};

// modifies rabbit.isSleeping
rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // undefined (no such property in the prototype)
```

📖：[Object properties configuration](https://javascript.info/object-properties)