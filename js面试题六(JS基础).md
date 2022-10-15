## <font color=SlateBlue>前端常见面试题(JS篇)六</font>

## 六、 ES6-ES2022新语法

## 6.1 ES6(ES2015)

### 1. 说说let和const

**let:**

- 声明变量
- 没有变量提升
- 不可重复声明
- 具有块级作用域
- 声明变量后可以在使用时赋值

**const:**

- 只读常量
- 没有变量提升
- 不可重复声明
- 具有块级作用域
- 声明变量后必须立马赋值

### 2. let、const、var的区别

**（1）块级作用域：** 块作用域由 `{ }`包括，let和const具有块级作用域，var不存在块级作用域。块级作用域解决了ES5中的两个问题：

- 内层变量可能覆盖外层变量
- 用来计数的循环变量泄露为全局变量

**（2）变量提升：** var存在变量提升，let和const不存在变量提升，即在变量只能在声明之后使用，否在会报错。

**（3）给全局添加属性：** 浏览器的全局对象是window，Node的全局对象是global。var声明的变量为全局变量，并且会将该变量添加为全局对象的属性，但是let和const不会。

**（4）重复声明：** var声明变量时，可以重复声明变量，后声明的同名变量会覆盖之前声明的遍历。const和let不允许重复声明变量。

**（5）暂时性死区：** 在使用let、const命令声明变量之前，该变量都是不可用的。这在语法上，称为**暂时性死区**。使用var声明的变量不存在暂时性死区。

**（6）初始值设置：** 在变量声明时，var 和 let 可以不用设置初始值。而const声明变量必须设置初始值。

**（7）指针指向：** let和const都是ES6新增的用于创建变量的语法。 let创建的变量是可以更改指针指向（可以重新赋值）。但const声明的变量是不允许改变指针的指向。

| **区别**           | **var** | **let** | **const** |
| ------------------ | ------- | ------- | --------- |
| 是否有块级作用域   | ×       | ✔️       | ✔️         |
| 是否存在变量提升   | ✔️       | ×       | ×         |
| 是否添加全局属性   | ✔️       | ×       | ×         |
| 能否重复声明变量   | ✔️       | ×       | ×         |
| 是否存在暂时性死区 | ×       | ✔️       | ✔️         |
| 是否必须设置初始值 | ×       | ×       | ✔️         |
| 能否改变指针指向   | ✔️       | ✔️       | ×         |

### 3. 解构赋值

**对象解构**

- 形式：`const { x, y } = { x: 1, y: 2 }`
- 默认：`const { x, y = 2 } = { x: 1 }`
- 改名：`const { x, y: z } = { x: 1, y: 2 }`

**数组解构**

- 规则：数据结构具有`Iterator接口`可采用数组形式的解构赋值
- 形式：`const [x, y] = [1, 2]`
- 默认：`const [x, y = 2] = [1]`

**函数参数解构**

- 数组解构：`function Func([x = 0, y = 1]) {}`
- 对象解构：`function Func({ x = 0, y = 1 } = {}) {}`

**应用场景:**

- 交换变量值：`[x, y] = [y, x]`
- 返回函数多个值：`const [x, y, z] = Func()`
- 定义函数参数：`Func([1, 2])`
- 提取JSON数据：`const { name, version } = packageJson`
- 定义函数参数默认值：`function Func({ x = 1, y = 2 } = {}) {}`
- 遍历Map结构：`for (let [k, v] of Map) {}`
- 输入模块指定属性和方法：`const { readFile, writeFile } = require("fs")`

**注意点**

- 匹配模式：只要等号两边的模式相同，左边的变量就会被赋予对应的值
- 解构赋值规则：只要等号右边的值不是对象或数组，就先将其转为对象
- 解构默认值生效条件：属性值严格等于`undefined`
- 解构遵循匹配模式
- 解构不成功时变量的值等于`undefined`
- `undefined`和`null`无法转为对象，因此无法进行解构

### 4. 字符串的新增方法

**includes**

返回布尔值，表示是否找到了参数字符串。

**startsWith**

返回布尔值，表示参数字符串是否在原字符串的头部。

**endsWith**

返回布尔值，表示参数字符串是否在原字符串的尾部。

**repeat**

`repeat`方法返回一个新字符串，表示将原字符串重复`n`次

### 5. 展开运算符

**（2）数组扩展运算符**

数组的扩展运算符可以将一个数组转为用逗号分隔的参数序列，且每次只能展开一层数组。

```javascript
const arr = [1, 2, 3, 4, 5, 6]
const newArr = [...arr] // 复制数组
const arr1 = ['two', 'three'];
const arr2 = ['one', ...arr1] // 合并数组
console.log(Math.max.call(null, ...arr)) // 将数组中的每一项作为参数使用
```

** (3) rest(剩余)参数**

扩展运算符被用在函数形参上时，**它还可以把一个分离的参数序列整合成一个数组**：

```javascript
function mutiple(...args) {
    let result = 1;
    for (var val of args) {
        result *= val;
    }
    return result;
}

mutiple(1, 2, 3, 4) // 24
```

### 6. 数组新增的方法

**Array.from()**

将类数组或者可迭代对象创建为一个新的数组，不改变原数组并返回这个新数组

**Array.of()**

**创建一个具有可变数量参数的新数组实例**，示例代码如下：

```javascript
Array.of(1) // [1]
Array.of(true, 1, '刘逍') // [true, 1, '刘逍']
```

**findIndex**

根据给定的回调函数，找到匹配的第一个元素的索引，找不到返回-1

**find**

根据给定的回调函数，找到匹配的第一个元素，找不到返回undefined

**fill**

将给定值填充数组，示例代码如下：

```js
const arr = [1, 2, 3, 4]
// 将给定值填充索引1-3
arr.fill('逍', 1, 3) // [ 1, '逍', '逍', 4 ]
```

**keys**

返回一个可迭代的对象，其内容为数组的key，示例代码如下：

```js
const arr = [1, true, '逍']
const keys = arr.keys()
for (const i of keys) {
  console.log(i) // 遍历结果 0 1 2
}
```

**values**

返回一个可迭代的对象，其内容为数组的value，示例代码如下：

```js
const arr = [1, true, '逍']
const values = arr.values()
for (const i of values) {
  console.log(i) // 遍历结果 1 true 逍
}
```

**entries**

返回一个可迭代的对象，其内容是一个数组，索引0为原数组的元素，1为原数组该位置的值，示例代码如下：

```js
const arr = [1, true, '逍']

const iterator = arr.entries()
console.log(Array.from(iterator)) // [ [ 0, 1 ], [ 1, true ], [ 2, '逍' ] ]
```

### 7. 对象新增方法

**Object.is() (用于解决NaN ≠= NaN，+0 === -0的问题)**

用于比较两个值是否相等，用于解决NaN ≠= NaN，+0 === -0的问题，示例代码如下：

```javascript
console.log(NaN === NaN) // false
console.log(+0 === -0) // true

console.log(Object.is(NaN, NaN)) // true
console.log(Object.is(+0, -0)) // false
```

**Object.assign()**

将**所有可枚举属性的值从一个或多个源对象复制到目标对象，并返回目标对象**，示例代码如下：

```js
const person = Object.assign({}, {name: '刘逍'}, {age: 18})
console.log(person) // { name: '刘逍', age: 18 }
```

**Object.getPrototypeOf()**

获取原型对象；

**Object.setPrototypeOf()**

设置原型对象。

### 8.class类

JS里的类就是构造函数的语法糖

**基本用法**

1. 类里面有个constructor函数,可以接收传递过来的参数,同时返回实例对象
2. constructor函数只要new生成实例时,就会自动调用这个函数,如果我们不写这个函数,类也会自动生成这个函数
3. 公共属性放在constructor中，公共方法直接在类里面写函数声明,会自动添加至原型对象中
4. class类没有变量提升,所以必须先定义类,才能通过类实例化对象
5. super()调用父类里的constructor方法,可以向里面传参,就等于Father.call(this,x,y)，super 必须在子类的this前面调用
6. class里面的方法的this指向的是调用者,如果调用者不是类的实例,就需要改变this的指向

```javascript
class Person {
    constructor(age) {
        // 属性
        this.myName = '刘逍'
        this.age = age
    }

    // 静态方法
    static print() {
        console.log()
    }

    // 访问器
    get myName() {
        console.log('getter')
        return this.myName
    }

    set myName(v) {
        console.log('setter' + v)
    }

    setName(v) {
        this.myName = v
    }
}
```

**关键词**

**constructor**

`constructor()`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor()`方法，如果没有显式定义，一个空的`constructor()`方法会被默认添加。

**super**

`super`这个关键字，既可以当作函数使用，也可以当作对象使用。

`super`作为函数调用时，代表父类的构造函数。子类的构造函数必须执行一次`super`函数。`super`虽然代表了父类`A`的构造函数，**但是返回的是子类`B`的实例**

`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

**getter、setter**

在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

**static**

如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

**# 私有属性**

`ES2022`正式为`class`添加了私有属性，方法是在属性名之前使用`#`表示。私有属性只能在类的内部使用（`this.#count`）。如果在类的外部使用，就会报错。

### 9. 模块化

ES6**中允许我们使用export导出模块，使用import引入模块**

### 10. Symbol

ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它属于 JavaScript 语言的原生数据类型之一

Symbol 值通过`Symbol()`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol
类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

### 11. Iterator

Iterator即迭代器，它是一种接口，为各种不同的数据结构提供了统一的访问机制，换句话说，只要有任何数据结构部署了迭代接口，就可以使用统一的方式的来遍历它。

实现可迭代接口的数据结构，一般都自身实现或继承了以`Symbol.iterator`属性的，就属于可迭代对象。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。

一个包含`next()`方法的对象，才可以称为一个迭代对象。`next()`对象的会有返回一个对象，对象中包含两个值，如下所示：

- `value`：迭代器返回的任何`JavaScript`值。`done`为`true`时可省略。
- `done`：一个布尔值，为`false`时表示迭代未停止，为`true`时立即停止迭代器，且可以省略`value`的值。

Iterator 的作用有三个：

1. 为各种数据结构，提供一个统一的、简便的访问接口；
2. 使得数据结构的成员能够按某种次序排列；
3. ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

### 12. for...of...循环

`for...of`循环，作为遍历所有数据结构的统一的方法。

一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员。也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`
方法。

`for...of`循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如`arguments`对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串。

### 13. Generator

Generator是ES2015中提供的一种异步编程解决方案，定义Generator函数在`function`关键字和函数名中间使用`*`星号，函数内部使用`yield`关键字定义不同的状态。

async的底层就是Generator函数

### 14. Proxy和Reflect

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

Proxy对象用于创建一个代理对象，从而实现基本操作的拦截和自定义，基本操作包含13种，如下表所示：

| 拦截 ⽅法                                   | 触发⽅式                                                     |
| ------------------------------------------- | ------------------------------------------------------------ |
| `get(target, propKey, receiver)`            | 读取某个属性                                                 |
| `set(target, propKey, value, receiver)`     | 写⼊某个属性                                                 |
| `has(target, propKey)`                      | `in`操作符                                                   |
| `deleteProperty(target, propKey)`           | `delete`操作符                                               |
| `getPrototypeOf(target)`                    | `Object.getPropertypeOf()`                                   |
| `setPrototypeOf(target, proto)`             | `Object.setPrototypeOf()`                                    |
| `isExtensible(target)`                      | `Object.isExtensible()`                                      |
| `preventExtensions(target)`                 | `Object.preventExtensions()`                                 |
| `getOwnPropertyDescriptor(target, propKey)` | `Object.getOwnPropertyDescriptor()`                          |
| `defineProperty(target, propKey, propDesc)` | `Object.defineProperty()`                                    |
| `ownKeys(target)`                           | `Object.keys()` 、`Object.getOwnPropertyNames()`、`Object.getOwnPropertySymbols()` |
| `apply(target, thisArg, args)`              | 调⽤⼀个函数                                                 |
| `construct(target, args)`                   | ⽤ new 调⽤⼀个函数                                          |

Vue3就是基于`Proxy`进行编写的

**Reflect**是ECMAScript2015提供的一个对象，它提供了一些拦截JavaScript操作的静态方法，这些方法与Proxy中的`handlers`中的方法一致。

Reflect并不是一个构造函数，也就是说它不能够被实例化。

`Proxy`对象中的每一个拦截操作（例如：`get`、`delete`等）,内部都对应的调用了`Reflect`的方法。它提供的静态方法与Proxy中的`handlers`中的方法名称都一致

### 15. Set、Map、WeakSet、WeakMap

`Set`、`Map`、`WeakSet`、`WeakMap`是ES2015中新增的几个对象：

**set**类似于数组，但是成员的值都是唯一的，没有重复的值。

`Set`本身是一个构造函数，用来生成 Set 数据结构。

`Set`和`WeakSet`与数组类似，准确的它他们是集合，这两者的区别就是`Set`可以存储任何数据类型，而`WeakSet`只能存储对象的引用，而且是弱引用；

`Set`对象在实际开发中最常见的就是实现数据去重，示例代码如下：

```js
const arr = [1, 2, 2, 3, 4, 3, 5]
const set = new Set(arr)
// set对象可以使用 ... 展开 所有项
console.log([...set]) // [ 1, 2, 3, 4, 5 ]
```

**map**类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash
结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

`Map`和`WeakMap`与对象类似，存储方式是键值对形式的，这两者的区别`Map`的键值对都是可以是任意的而`WeakMap`键必须是对象的引用而值可以是任意类型的。

## 6.2 ES2016

### 1. 指数运算符

ES2016中新增指数`**`，也叫幂运算符，与Math.pow()有着一样的功能，示例代码如下：

```js
console.log(2 ** 10 === Math.pow(2, 10)) // true
```

### 2. Array.prototype.includes()方法

在ES2016中在数组原型上增加了`includes()`方法，该方法用于判断一个数组中是否包含指定的值，返回一个布尔值，示例代码如下：

```js
const arr = [1, 2, 3, 4, 5, NaN]
console.log(arr.indexOf(NaN)) // -1
console.log(arr.includes(NaN)) // true
```

值得注意的是使用`includes()`时`NaN`与`NaN`、`+0`与`-0`是相等的。

## 6.3 ES2017

### 1. 对象新增方法

- `Object.values()`：返回一个给定对象自身的所有可枚举属性值的数组；
- `Object.entries()`：返回一个给定对象自身可枚举属性的键值对数组；
- `Object.getOwnPropertyDescriptors()`：返回给定对象所有自有属性的属性描述符。

### 2. 字符串新增方法

- `padStart()`：在字符串开头填充空格；
- `padEnd()`：在字符串结尾填充空格；

## 6.4 ES2018

### 1. 异步迭代

在ES2018中新增了`for await...of`语句，该用于可以遍历异步可迭代对象

### 2.**对象扩展运算符**

对象的扩展运算符(...)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中。

```js
let bar = { a: 1, b: 2 };
let baz = { ...bar }; // { a: 1, b: 2 }
```

上述方法实际上等价于:

```js
let bar = { a: 1, b: 2 };
let baz = Object.assign({}, bar); // { a: 1, b: 2 }
```

`Object.assign`方法用于对象的合并，将源对象`（source）`的所有可枚举属性，复制到目标对象`（target）`。`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。(**
如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性**)

### 3. Promise.prototype.finally

`finally()`方法会返回一个`Promise`对象，当promise的状态变更，不管是变成`rejected`或者`fulfilled`，最终都会执行`finally()`的回调。

## 6.5 ES2019

### 1. `try...catch`：语句中的`catch`允许不使用参数

### 2. trimStart、trimLeft、trimEnd、trimRight

- `String.prototype.trimStart`：用于去除字符串左边的空格；
- `String.prototype.trimLeft`：它是`trimStart`的别名
- `String.prototype.trimEnd`：用于去除字符串右边的空格；
- `String.prototype.trimRight`：它是`trimEnd`的别名

### 3. Object.fromEntries

`Object.fromEntries()`方法把键值对列表转换为一个对象，是`Object.entries()`方法的反操作

## 6.6 ES2020

### 1. 动态导入

动态导入，也就是我们需要该模块的时候才会进行加载，这可以减少开销和页面加载时间，示例代码如下：

```js
import('/modules/my-module.js').then(module => {
  // Do something with the module.
})
```

动态导入使用`import()`方法，它返回一个Promise。

在ES2020中，还为`import`增加一个`meta`对象，该对象给JavaScript模块暴露了特定上下文的元数据属性的对象。

### 2. BigInt数据类型

BigInt的出现时解决JavaScript中允许的最大数字是`2**53-1`的问题，`BigInt` 可以表示任意大的整数。

### 3. 空值合并运算符 ??

该运算符与逻辑或运算符类似。其计算规则为，只要左运算元为`null`或者`undefined`，则返回右运算元，否则返回左运算元。而逻辑或运算符只有左运算元转换为`boolean`类型后为`false`，就返回右运算元。

### 4. 可选链操作符 ?.

`?.` 操作符的功能类似于 `.`
链式操作符，不同之处在于，在引用为空 (`null`
或者 `undefined`)
的情况下不会引起错误，该表达式短路返回值是 `undefined`。

当我们访问某个属性时，只要有一处不存在，就会返回`undefind`，不会报错。

```js
var A = {}
// console.log(A.a.b) // 报错
console.log(A.a?.b) // undefined
```

可选链操作符也可用于对象下方法的调用，示例代码如下：

```js
var obj = {}

// 如果存在 obj.fun() 这个方法，下面则会直接调用，如果不存在则会返回undefined
obj.fun?.A()
```

## 6.7 ES2021

### 1. String.prototype.replaceAll

`replaceAll()`方法返回一个新字符串，新字符串的内容是经过替换的，实例代码如下：

```js
const str = '刘逍'
const newStr = str.replaceAll('逍', '小')
console.log(newStr) // 刘小
```

### 2. 数值分隔符 _

严格意义上讲数值分隔符(`_`)并不属于一个运算符，其作用就是使数字更加利于阅读，例如下面的代码

```js
console.log(1_0000_0000) // 100000000
```

### 3. Promise.any()

ES2021中新增的`Promise.any()`方法，它接受的参数和与`promise.all()`是一致的，唯一不同的是，`Promise.any()`
方法接受的可迭代对象中没有一个promise成功（即所有的promises都失败/拒绝），就返回一个失败的promise和AggregateError类型的实例。

### 4. 逻辑赋值操作符 &&= 、||= 、？？=

```js
const [f1, f2, f3] = [true, false]
f1 &&= '逍' // 等同于 str = str && '逍'
f2 ||= '逍' // 等同于 str = str || '逍'
f3 ??= '逍' // 等同于 str = str ?? '逍'
```

## 6.8 ES2022

### 1. class的扩展

在ES2022中允许我们并不在`constructor`中定义类的成员，示例代码如下：

```javascript
class C {
    myName = '刘逍'
}
/* 两者是一致的 */
class C {
    constructor() {
        myName = '刘逍'
    }
}
```

ES2022中允许我们使用`#`开头命名的变量作为类的私有成员

### 2. await在顶层使用

在ES2022中新增了允许在顶层使用`await`，在顶层可以不适用`async`函数进行包裹，示例代码如下：

```javascript
import {AsyncFun} from 'module'

await AsyncFun()
console.log(123)
```

### 3. Object.hasOwn()

`Object.hasOwn()`方法用于判断某个对象上是否具有某个属性，示例代码如下：

```javascript
const person = {
    name: '刘逍',
    age: 18,
}
console.log(Object.hasOwn(person, 'name')) // true
console.log(Object.hasOwn(person, 'sex')) // false
```

### 4. Array.prototype.at()

ES2022中新增的`at()`方法，它的作用是获取数组中的某个成员，它的参数是数组的索引，与直接使用索引的方式不同，它允许我们传递负值，等同于从后面倒数，示例代码如下：

```js
const arr = [1, 2, 3, 4, 5, 6]
console.log(arr.at(-1)) // 6
// 等同于 arr[arr.length - 1]
```

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.16 |
