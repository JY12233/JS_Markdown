# 浅拷贝

## 定义与本质

浅拷贝（Shallow Copy）是指创建一个新的对象，这个新对象有着原始对象属性值的一份精确拷贝。

- 基本数据类型

  如果属性是基本数据类型（如字符串、数字、布尔值等），拷贝的是该属性的值本身。

- 引用数据类型

  如果属性是引用数据类型（如对象、数组、函数等），拷贝的仅仅是该属性的内存地址（引用指针）。这意味着，原始对象和新对象中的该属性指向的是内存中的同一个对象。

## 核心作用

- 数据隔离与保护

  在函数传参或状态管理中，通过浅拷贝生成新对象，可以避免直接修改原始数据，从而防止意外的副作用。

- 性能与内存优化

  相比于深拷贝（Deep Copy），浅拷贝只复制第一层级的属性。对于深层嵌套的对象，浅拷贝不会递归复制内部对象，因此执行速度更快，内存占用更低。

- 不可变数据模式

  在 React 等现代前端框架中，浅拷贝常被用于生成新的状态对象，以触发视图的重新渲染。

## 常用实现方式

### 扩展运算符（`...`）

这是现代 JavaScript 中最简洁的浅拷贝方式，适用于数组和对象。

```javascript
const originalObj = { a: 1, b: { c: 2 } };
const copiedObj = { ...originalObj };
```

### `Object.assign()`

用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。

```javascript
const originalObj = { a: 1, b: { c: 2 } };
const copiedObj = Object.assign({}, originalObj);
```

### 数组专用方法

针对数组，可以使用以下方法返回一个新的数组副本：

- `Array.prototype.slice()`：`const newArr = oldArr.slice();`
- `Array.prototype.concat()`：`const newArr = [].concat(oldArr);`
- `Array.from()`：`const newArr = Array.from(oldArr);`

## 局限性

浅拷贝仅复制对象的第一层属性。如果原始对象的属性值是嵌套的对象或数组，修改新对象中嵌套属性的值，会同步影响到原始对象。

```javascript
const original = {
  name: 'Alice',
  address: { city: 'Beijing' } // 嵌套对象
};

const copy = { ...original };

// 修改基本数据类型：互不影响
copy.name = 'Bob';
console.log(original.name); // 输出: Alice

// 修改引用数据类型：互相影响
copy.address.city = 'Shanghai';
console.log(original.address.city); // 输出: Shanghai（原始对象被修改）
```

## 注意事项

- `Object.assign()` 的陷阱

  `Object.assign()` 的第一个参数必须是目标对象。如果传入 `null` 或 `undefined`，会抛出错误。通常使用空对象 `{}` 作为第一个参数。

- 原型链属性

  `Object.assign()` 和扩展运算符只会拷贝对象自身的可枚举属性，不会拷贝继承的属性或不可枚举的属性。

- 特殊对象

  对于 `Date`、`RegExp`、`Map`、`Set` 等特殊对象，浅拷贝可能会丢失其内部状态或原型方法，需谨慎处理。

# 深拷贝

## 定义与本质

深拷贝（Deep Copy）是指将一个对象从内存中完整地拷贝一份出来，从堆内存中开辟一个新的区域存放新对象。

- 完全独立

  深拷贝会递归地复制原数据的所有层级。新对象与原对象在内存中是完全独立的两个个体，没有任何引用关联。

- 互不影响

  修改新对象的任何属性（包括深层嵌套的属性），都不会对原对象产生任何影响。

## 核心作用

- 数据完全隔离

  在处理复杂表单数据、状态管理（如 Redux、Vue/React 状态）或数据缓存时，确保对副本的修改不会意外污染原始数据源。

- 生成数据快照

  在需要保留数据历史状态或进行数据回滚的场景中，深拷贝能够生成一份完全独立的数据快照。

- 避免引用陷阱

  在传递复杂嵌套对象时，彻底切断引用关系，防止因共享内存地址而引发的逻辑错误。

## 常用实现方式

1. `JSON.parse(JSON.stringify())`

   这是最简单的原生深拷贝方法，通过将对象序列化为 JSON 字符串再解析回对象来实现。

   - 适用场景

     仅包含纯 JSON 数据（如数字、字符串、布尔值、数组、普通对象）的简单对象。

   - 局限性

     - 无法拷贝函数（Function）。

     - 无法拷贝 `undefined`、`Symbol` 类型。

     - 无法处理循环引用（会抛出错误）。

     - `Date` 对象会被转换为字符串，`RegExp` 和 `Error` 对象会变成空对象 `{}`。

2. `structuredClone()` API

   这是现代浏览器和 Node.js 原生支持的全局方法，使用结构化克隆算法。

   - 适用场景

     现代 Web 应用，支持循环引用、`Date`、`RegExp`、`Map`、`Set`、`ArrayBuffer` 等多种复杂数据类型。

   - 局限性

     不支持拷贝函数（会抛出 `DataCloneError`）；属于 Web API 而非 JavaScript 语言核心特性，极老旧的浏览器可能不支持。

3. 递归手写实现

   通过自定义函数递归遍历每一层数据，手动判断类型并复制。

   - 适用场景

     需要高度定制化拷贝逻辑，或需要兼容极老旧环境且不能引入第三方库时。

   - 核心逻辑

     判断当前值是否为对象或数组，若是则创建新容器并递归拷贝内部元素；同时需使用 `WeakMap` 解决循环引用问题。

4. 方式四：第三方工具库

   使用成熟的工具库（如 Lodash 的 `_.cloneDeep()` 或 Immer 的 `produce()`）。

   - 适用场景

     企业级生产环境，需要处理各种边缘情况（如特殊对象、循环引用、原型链保留等）。

   - 优势

     经过充分测试，兼容性好，功能全面，开发成本低。

## 浅拷贝的区别

| 对比维度     | 深拷贝（Deep Copy）                              | 浅拷贝（Shallow Copy）                         |
| ------------ | ------------------------------------------------ | ---------------------------------------------- |
| **内存共享** | 完全不共享，新对象拥有独立内存。                 | 仅外层独立，嵌套的复杂类型共享内存。           |
| **修改影响** | 修改新对象，原对象完全不受影响。                 | 修改嵌套的复杂类型，原对象会同步变化。         |
| **性能开销** | 较高（需递归复制所有层级）。                     | 较低（仅复制第一层）。                         |
| **适用场景** | 需完全独立操作嵌套数据（如复杂表单、状态管理）。 | 仅操作外层数据，不修改嵌套内容（如简单列表）。 |

## 代码示例

```javascript
const original = {
  name: 'Alice',
  address: { city: 'Beijing' },
  hobbies: ['reading', 'swimming']
};

// 1. 使用 JSON 方法（简单场景）
const jsonCopy = JSON.parse(JSON.stringify(original));
jsonCopy.address.city = 'Shanghai';
console.log(original.address.city); // 输出: Beijing（原对象未受影响）

// 2. 使用 structuredClone（现代浏览器推荐）
const structuredCopy = structuredClone(original);
structuredCopy.hobbies.push('running');
console.log(original.hobbies); // 输出: ['reading', 'swimming']（原对象未受影响）
```

## 注意事项

- 性能考量

  深拷贝由于需要递归复制所有层级，对于包含大量元素或深层次嵌套的对象，会显著增加 CPU 和内存开销。在性能敏感的场景中，若非必要应避免频繁使用。

- 函数处理

  如果对象中包含函数，`JSON.parse(JSON.stringify())` 和 `structuredClone()` 均无法正确处理。此时应考虑使用 Lodash 的 `_.cloneDeep()` 或手动递归实现。

- 循环引用

  在手动实现深拷贝时，必须使用 `WeakMap` 等数据结构检测并处理循环引用，否则会导致无限递归和栈溢出。

# this 指向处理

## this 的核心原则

JavaScript 中的 `this` 关键字不是在函数定义时决定的，而是在函数调用时决定的。`this` 的指向完全取决于函数的调用方式，而非函数定义的位置。

## this 的五种绑定规则

理解 `this` 指向的核心在于掌握以下五种绑定规则及其优先级：

- 默认绑定（独立调用）

  当函数作为独立函数被调用时（如 `fn()`），`this` 指向全局对象（浏览器中为 `window`，Node.js 中为 `global`）。在严格模式（`'use strict'`）下，`this` 为 `undefined`。

- 隐式绑定（对象方法调用）

  当函数作为对象的方法被调用时（如 `obj.fn()`），`this` 指向调用该方法的对象（即点号前面的对象）。

- 显式绑定（call / apply / bind）

  通过调用函数的 `call`、`apply` 或 `bind` 方法，可以强制指定函数执行时的 `this` 指向。

- new 绑定（构造函数调用）

  当使用 `new` 关键字调用函数时，`this` 指向新创建的实例对象。

- 箭头函数（词法 this）

  箭头函数没有自己的 `this`，它的 `this` 是继承自定义时的外层作用域（词法作用域），且一旦绑定无法被任何方式修改。

> **绑定优先级**
>
> new 绑定 > 显式绑定 > 隐式绑定 > 默认绑定。

## 常用方法

### 箭头函数

箭头函数不创建自己的 `this`，而是继承外层代码块的 `this` 值。这是现代 JavaScript 中最直观、最常用的解决 `this` 指向问题的方法，尤其适用于回调函数和事件处理器。

```javascript
const obj = {
  name: 'Alice',
  greet: function() {
    // 箭头函数继承外层 greet 方法的 this，即 obj
    setTimeout(() => {
      console.log(this.name); // 输出: Alice
    }, 1000);
  }
};
```

### `bind`

`bind()` 方法会创建一个**新函数**，并将 `this` 永久绑定到指定的对象。这种方法常用于 React 类组件中绑定事件处理器，或在需要将方法作为回调传递时保持 `this` 指向。

```javascript
const obj = {
  name: 'Bob',
  greet: function() {
    console.log(this.name);
  }
};
const boundGreet = obj.greet.bind(obj);
boundGreet(); // 输出: Bob
```

### `call` 或 `apply`

这两个方法可以立即调用函数并指定 `this` 的指向。区别在于参数传递方式：`call` 逐个传递参数，`apply` 以数组形式传递参数。

```javascript
function introduce(city) {
  console.log(`${this.name} lives in ${city}`);
}
const user = { name: 'Charlie' };
introduce.call(user, 'Beijing'); // 输出: Charlie lives in Beijing
introduce.apply(user, ['Shanghai']); // 输出: Charlie lives in Shanghai
```

### 在构造函数中绑定

在类的构造函数中绑定方法，可以确保无论该方法在哪里被调用，`this` 都正确指向类实例。

```javascript
class Car {
  constructor() {
    this.name = 'Ferrari';
    // 在构造函数中绑定 sayHi 方法
    this.sayHi = this.sayHi.bind(this);
  }
  sayHi() {
    console.log(`Hello from ${this.name}`);
  }
}
```

### 闭包

在 ES6 之前，常用这种方式解决 `this` 指向问题。通过在外部函数中创建一个变量（如 `self` 或 `that`）来保存当前的 `this` 引用，然后在内部函数中使用该变量。

```javascript
const obj = {
  name: 'David',
  greet: function() {
    const self = this; // 保存 this 引用
    setTimeout(function() {
      console.log(self.name); // 输出: David
    }, 1000);
  }
};
```

## 隐式丢失

当对象的方法被赋值给变量后再调用时，`this` 的绑定会丢失，退化为默认绑定。

```javascript
const obj = {
  name: 'Eve',
  sayHi: function() {
    console.log(this.name);
  }
};
const lostFn = obj.sayHi;
lostFn(); // 非严格模式下输出 undefined（this 指向 window，window.name 为空）
```

可使用 `bind` 绑定（`const boundFn = obj.sayHi.bind(obj)`），或使用箭头函数包裹。

## DOM 事件中的 this

在 DOM 事件处理函数中，`this` 指向绑定事件的 DOM 元素本身。

```javascript
document.querySelector('button').addEventListener('click', function() {
  console.log(this); // 输出: <button> 元素
});
```

# 防抖

## 定义

防抖（Debounce）是一种用于限制函数执行频率的优化技术。其核心原理是：在事件被触发后，不立即执行对应的回调函数，而是设置一个定时器。如果在定时器规定的时间内事件再次被触发，则**重新计时**；只有当定时器规定的时间内事件不再被触发，回调函数才会真正执行。

## 核心作用

防抖主要用于处理高频触发的事件，以避免不必要的性能开销和资源浪费。

- 减少网络请求

  防止用户连续点击按钮或快速输入搜索关键词时，向后端发送大量重复的 API 请求。

- 优化 DOM 操作

  避免在窗口大小调整（resize）或页面滚动（scroll）等高频事件中频繁执行复杂的 DOM 计算或样式修改，从而防止页面卡顿。

- 表单验证

  在用户输入过程中实时进行表单校验，但仅在用户停止输入后才发送验证请求，提升用户体验。

## 实现原理

防抖的实现依赖于 JavaScript 的闭包和定时器（`setTimeout` / `clearTimeout`）。

1. 利用闭包保存一个定时器变量（`timer`）。
2. 每次事件触发时，先清除上一次未执行的定时器（`clearTimeout(timer)`）。
3. 重新设置一个新的定时器，将需要执行的回调函数放入其中。
4. 只有当最后一次触发后，经过指定的等待时间，定时器才会执行回调。

## 代码实现

### 基础版

```javascript
function debounce(fn, delay) {
  let timer = null; // 利用闭包保存定时器
  return function(...args) {
    // 每次触发时清除上一次的定时器
    if (timer) clearTimeout(timer);
    // 重新设置定时器
    timer = setTimeout(() => {
      fn.apply(this, args); // 使用 apply 保持 this 指向和参数传递
    }, delay);
  };
}
```

### 带立即执行功能

在某些场景下（如按钮点击），希望第一次触发时立即执行，后续触发则需等待。

```javascript
function debounce(fn, delay, immediate = false) {
  let timer = null;
  return function(...args) {
    if (timer) clearTimeout(timer);
    if (immediate && !timer) {
      // 第一次触发且没有定时器时，立即执行
      fn.apply(this, args);
    }
    timer = setTimeout(() => {
      if (!immediate) fn.apply(this, args);
      timer = null; // 执行完毕后重置定时器
    }, delay);
  };
}
```

## 使用示例

### 搜索框输入

```javascript
const searchInput = document.querySelector('#search');

function handleSearch(keyword) {
  console.log('发送搜索请求:', keyword);
}

// 用户停止输入 500ms 后才执行搜索
const debouncedSearch = debounce(handleSearch, 500);
searchInput.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});
```

### 窗口大小调整

```javascript
function handleResize() {
  console.log('窗口大小已改变，执行重新布局...');
}

// 用户停止调整窗口大小 300ms 后才执行布局计算
window.addEventListener('resize', debounce(handleResize, 300));
```

## 与节流的区别

- 防抖（Debounce）

  关注“最后一次”。只有在事件停止触发一段时间后才执行，适用于搜索输入、表单验证等场景。

- 节流（Throttle）

  关注“固定频率”。无论事件触发多频繁，都保证在固定的时间间隔内至少执行一次，适用于滚动加载、拖拽动画等场景。

# 节流

## 定义

节流（Throttle）是一种用于限制函数执行频率的性能优化技术。其核心原理是：在连续触发的事件中，规定在一个固定的时间间隔内，目标函数最多只执行一次。无论事件触发得多么频繁，函数的执行都会被“稀释”为均匀的、有规律的节奏。

## 作用

节流主要用于处理需要持续反馈的高频事件，在保证功能正常运行的同时，显著降低系统资源消耗。

- 优化滚动与拖拽体验

  在页面滚动（scroll）或元素拖拽（mousemove）时，限制位置计算或 DOM 更新的频率，避免因主线程被长时间占用而导致页面卡顿。

- 控制网络请求频率

  在无限滚动加载等场景中，限制检查滚动位置并发起请求的频率，防止短时间内发送大量无效请求。

- 防止按钮重复点击

  限制提交按钮的点击频率，确保在设定的冷却时间内，即使用户快速多次点击，表单也只会提交一次。

## 实现原理

节流的实现主要依赖两种策略：时间戳和定时器。

- 时间戳策略

  记录上一次函数执行的时间戳。每次事件触发时，计算当前时间与上次执行时间的差值。如果差值大于或等于设定的时间间隔，则立即执行函数并更新时间戳。

- 定时器策略

  利用 `setTimeout` 维护一个延时任务。首次触发时启动定时器，后续触发若定时器未执行完毕则忽略。待定时器回调执行后，清空定时器标记，允许下一轮触发。

## 代码实现

### 时间戳版（首次立即执行）

```javascript
function throttle(fn, interval) {
  let lastTime = 0; // 记录上一次执行的时间戳
  return function(...args) {
    const now = Date.now();
    // 如果当前时间与上次执行时间的差值大于等于间隔，则执行
    if (now - lastTime >= interval) {
      lastTime = now;
      fn.apply(this, args);
    }
  };
}
```

### 定时器版（首次延迟执行）

```javascript
function throttle(fn, interval) {
  let timer = null;
  return function(...args) {
    // 如果当前没有正在运行的定时器，则设置一个新的
    if (!timer) {
      timer = setTimeout(() => {
        fn.apply(this, args);
        timer = null; // 执行完毕后清除定时器，允许下一次触发
      }, interval);
    }
  };
}
```

### 混合版

结合时间戳和定时器的优点，确保第一次触发立即执行，且在连续触发停止后，最后一次触发也能被执行。

```javascript
function throttle(fn, delay) {
  let lastTime = 0;
  let timer = null;
  return function(...args) {
    const now = Date.now();
    const remaining = delay - (now - lastTime);
    // 如果距离上次执行时间已超过 delay，或修改了系统时间
    if (remaining <= 0 || remaining > delay)
      if (timer) {
        clearTimeout(timer);
        timer = null;
      }
      lastTime = now;
      fn.apply(this, args);
    } else if (!timer) {
      // 如果处于冷却期且没有定时器，设置定时器保证最后一次执行
      timer = setTimeout(() => {
        lastTime = Date.now();
        timer = null;
        fn.apply(this, args);
      }, remaining);
    }
  };
}
```

## 使用示例

### 滚动加载更多

```javascript
function loadMore() {
  console.log('加载更多数据...');
}

// 无论用户滚动多快，最多每 500ms 触发一次加载
const throttledLoad = throttle(loadMore, 500);
window.addEventListener('scroll', throttledLoad);
```

### 游戏技能冷却

```javascript
function castSkill() {
  console.log('释放技能');
}

// 限制技能释放频率，每 1000ms 最多释放一次
const throttledSkill = throttle(castSkill, 1000);
attackButton.addEventListener('click', throttledSkill);
```

## 与防抖的区别

- 节流（Throttle）

  关注“固定频率”。无论事件触发多频繁，都保证在固定的时间间隔内至少执行一次，适用于滚动加载、拖拽动画等需要持续反馈的场景。

- 防抖（Debounce）

  关注“最后一次”。只有在事件停止触发一段时间后才执行，适用于搜索输入、表单验证等只关心最终结果的场景。