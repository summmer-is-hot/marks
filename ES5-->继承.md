##	<font color=SlateBlue>大多数前端工程师不了解的ES5继承</font>

## 前文再续，接上一回 原型

## 4. 面向对象ES5 -> 继承

- 举个栗子，把继承抽象成现实的栗子。
    - 首先我们是人类，在生活中我们扮演着各种角色，每个年龄段扮演着不同的角色。当我们读书期间我们扮演着学生这一角色，当我们踏入社会参加工作的那一刻，我们扮演着员工这一角色，如果有幸各位当老板了，扮演着老板这一角色，但是无论扮演着什么角色，我们终究是人类（**共性，代码亦是如此，抽取共享，提高代码复用性**）。谁也逃离不了，生老病死，我们要在活着的时候多做一些有意义的事，日行一善，多帮助他人，回馈社会。
- 面向对象之四大特性，我们主要来研究继承，将复用的代码抽取到父类中，子类继承父类直接使用即可，子类可以使用父类中的方法或属性了。
    - 封装
    - 继承
    - 多态
    - 抽象

### 4.1 原型链

那JS中如何实现继承呢？我们前面说过，其实JS中本没“类”，模仿的多了，也就有了“类”，而在JS中，实现继承，是逃不开原型链的，因为正是利用原型链这一机制，实现了“类”

- 前面我们已经在对象的隐式原型中，谈到了原型链，现在我们要对它进行剖析了

```javascript
let info = {
  name: 'ice',
  age: '22'
}

console.log(info.__proto__) //Object.prototype

console.log(info.__proto__.__proto__) //null
```

- 最开始说过，一个对象查找属性（key）的时候，首先从自己本身查找，如果有直接返回值（value），如果没有则沿着原型链查找，原型链中有则返回，直至找到“尽头”(`Object.prototype`)，还未找到，则返回undefined。

```js
let info = {
  name: 'ice',
  age: '22'
}

info.__proto__ = {
  money: 100
}

info.__proto__.__proto__ = {
  website: 'iceweb.icu'
}

console.log(info.name) //ice
console.log(info.money) //100
console.log(info.website) //iceweb.icu
console.log(info.girlFriend) // undefined
```

- 只要你想你可以一直定义对象的隐式原型(`__proto__`)，让它一直查找，（因为`info`的隐式原型又是一个对象，对象又有隐式原型，这样一直查找下去），这样一层一层像链条一样，所以我们把它称之为**原型链**。这就是JS中实现继承的本质。

#### 4.1.1 查看顶层原型（原型链尽头）

- 默认情况下: 顶层原型为`Object.prototype`，再继续查找下去就为null了，也可以说Object是所有类的父类

#### 4.1.2 顶层原型有什么用？

- `Object.prototype有什么用呢？`
    - 该对象上默认有许许多多的属性和方法，创建出来的对象继承自`Object.prototype`，所以通过字面量创建出来的对象/`new Object()`都能使用顶层原型上的属性和方法。如：toString()...

```javascript
const info1 = {
  name: 'ice',
  age: 22
}

const info2 = new Object({
  name: 'panda',
  age: 20
})

console.log(info1.name) //ice
console.log(info1.valueOf() === info1) //true
console.log(info1.toString()) //[object Object]

console.log(info2.name) //ice
console.log(info2.valueOf() === info2) //true
console.log(info2.toString()) //[object Object]
```

**创建对象的原型图**

![image.png](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/prototype_4.webp?)

### 4.2 实现继承的多种办法

#### 4.2.1 引用赋实现(大聪明做法)

```javascript
//父类
function Person() {}
//在父类
Person.prototype.running = function() {
  console.log(`${this.name} running!!!`)
}

function Student(name, age) {
  this.name = name
  this.age = age
}

//引用赋值，此时子类的原型对象 === 父类的原型对象
Student.prototype = Person.prototype
Student.prototype.studying = function() {
  console.log(`${this.name} studying!!!`)
}

const ice = new Student('ice', 22)

ice.running()
ice.studying()

//ice running!!!
//ice studying!!!
```

- “看起来”好像是实现了继承，确实“使用”子类/父类的方法，但是真的是这样吗？
- 因为父类与子类用的是一个原型对象，无论我操作子类/父类 它们互相影响

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}

Person.prototype.running = function() {
  console.log(`${this.name} running!!!`)
}

function Student(name, age) {
  this.name = name
  this.age = age
}

Student.prototype = Person.prototype
Student.prototype.studying = function() {
  console.log(`${this.name} studying!!!`)
}

const p1 = new Person('panda', 20) 

//被子类操作后，父类也有了studying方法，显然是错误的做法
p1.studying()
```

- 我们再仔细看，明明`Person`和`Stuedent`都有的`name`/`age`属性，我们却要定义两遍，我们能不能进行属性复用呢？完了第一个方法的继承都没解决，又来了一个属性复用的问题...,别着急让我们一起来探究~

#### 4.2.2 原型链继承

```javascript
//1. 父类
function Person(name, age) {
  this.name = name
  this.age = age
  this.sex = 'male'
}

//2. 父类显示原型上添加方法
Person.prototype.running = function() {
  console.log(`${this.name} running!!!`)
}

//3. 子类
function Student(name, age) {
  this.name = name
  this.age = age
}

//4. 创建父类的实例，这个对象`p1.__proto__ === Person.prototype`
const p1 = new Person()

//5. 子类显示的原型对象 = p1对象
Student.prototype = p1

//6. 在p1对象上添加方法
Student.prototype.studying = function() {
  console.log(`${this.name} studying!!!`)
}

const s1 = new Student('ice', 22)
console.log(s1)
console.log(s1.sex)

s1.running()
s1.studying()

//Person { name: 'ice', age: 22 }
//male
//ice running!!!
//ice studying!!!
```

- 这样是初步的实现了继承，但是它们存在几个问题
    - 当我打印s1对象的时候，有一些值(`sex`)，是存在p1对象上的，我直接打印却查询不到这个属性，但是我通过`s1.sex`却能访问到，容易产生误解
    - s1的类型错误，具体的类型居然为`Person`，应该是`Student`类型
    - 没有进行属性的复用，父类有的属性，子类竟然还重复定义了一次

**内存表现**

![image.png](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/prototype_5.webp?)

- 其本质两点概括
    - 创建了一个全新的对象（`p1`），且这个对象的隐式原型指向了父类的显示原型
    - 子类的显示原型指向那个新创建来的对象
        - 当我new子类的时候，其创建的实例的隐式原型，已经指向了新创建出来的对象（`p1`），沿着新对象的隐式原型，从而实现了继承

#### 4.2.3 借用构造函数继承(经典继承)

- 借用构造函数继承主要解决的问题就是复用父类属性，这里就非常容易理解，废话少说我们直接看代码。

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}

function Student(name, age) {
  Person.apply(this, [name, age])
}

const s1 = new Student('ice', 22)
console.log(s1) 

//{ name: 'ice', age: 22 }
```

- 借用，其实就是主动的调用一下父类，通过改变this的指向，变相的实现父类属性的复用，有关this问题就不再赘述了，可以看下我之前写的this文章，这里用apply/call都可以，传参形式不同而已。

#### 4.2.4 组合继承

- 组合继承则是原型链继承+借用构造函数继承的结合

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}

Person.prototype.running = function() {
  console.log(`${this.name} running!!!`)
}

function Student(name, age) {
  Person.apply(this, [name, age])
}

const p1 = new Person()

Student.prototype = p1

Object.defineProperty(Student.prototype, "constructor", {
  value: Student,
  writable: true,
  configurable: false,
  enumerable: false
})

Student.prototype.studying = function() {
  console.log(`${this.name} studying!!!`)
}

const s1 = new Student('ice', 22)
console.log(s1)

s1.running()
s1.studying()

// Student { name: 'ice', age: 22 }
// ice running!!!
// ice studying!!!
```

- 组合继承是js中最常用的模式之一，到了这里，继承的基本实现已经没有了什么问题了，但是它仍然有一些不完美的地方
- 缺点:
    - 最大的问题就是，父类都会被调用两次，第一次是构建`p1`对象的时候，第二次是借用构造函数实现父类属性复用的时候。

#### 4.2.5 原型式继承函数

- 最早为前端著名大神道格拉斯提出，`JSON`的创立者，对就是前后端进行交互传输的`JSON`, JS语言的创始人Brendan Eich称道格拉斯为JS界的宗师。
- 与寄生式继承函数类似的一种思想

**1. 兼容性最好的写法**

```javascript
function create(obj) {
  function Fn() {}
  Fn.prototype = obj
  
  return new Fn()
}
```

**2. setPrototypeOf方法**

- 方法设置一个指定的对象的原型
- 不推荐使用，由于现代 JavaScript 引擎优化属性访问所带来的特性的关系，更改对象的 `[[Prototype]]` 在各个浏览器和 JavaScript 引擎上都是一个很慢的操作。

```javascript
function create(obj) {
  let newObj = {}
  Object.setPrototypeOf(newObj, obj)
  
  return newObj
}

```

**3. create方法**

```javascript
function create(obj) {
  let newObj = Object.create(obj)
  
  return newObj
}
```

- 方法用于创建一个新对象，使用现有的对象来作为新创建对象的原型`[[prototype]]`
- 其本质跟第一种方式没有区别

**4. `__proto__`写法，不考虑兼容性**

```js
function create(obj) {
  let newObj = {}
  newObj.__proto__ = obj
  
  return newObj
}
```

- 总结
    - 原型式继承函数的本质
        - 创建一个空对象
        - 这个空对象的隐式原型`[[prototype]]`，等于父类的显示原型，最后返回这个对象

#### 4.2.6 寄生式继承函数

```javascript
//1. 原型式继承函数
function create(obj) {
  function Fn() {}
  Fn.prototype = obj
  return new Fn()
}

//父类
function Person() {}
Person.prototype.running = function() {
  console.log('running!!!')
} 

//2. 寄生式继承函数
function createStu(person) {
  const s1 = create(person) 
  
  s1.studying = function() {
    console.log('Studying code')
  }
  return s1
}

//3. 子类对象
const s1 = createStu(Person.prototype)
s1.running()
```

- 在原型式继承函数的基础上又进行了一次封装，在函数内部来增强对象，最后将这个对象返回

#### 4.2.7 寄生式组合继承

- 组合继承是比较理想的继承方案，但是最大的问题最少要调用父构造函数两次，但是寄生式组合继承就能解决这一问题
- 寄生式组合继承，就成是利用寄生式继承函数(与原型式继承函数类似)，像寄生虫一样，寄生在另外的对象上。

```javascript
function create(o) {
  function F() {}
  F.prototype = o
  return new F() 
}
//1. 创建一个新对象  
//2. 新对象 __proto__ 指向父类的prototype
//3. 最后返回这个对象

function inherit(SubType, SuperType) {
  //子类的prototype = 新创建出来的对象
  SubType.prototype = create(SuperType.prototype)
  Object.defineProperty(SubType.prototype, 'constructor', {
    writable: true,
    configurable: false,
    enumerable: false,
    value: SubType
  })
}

//父类
function Person(name, age) {
  this.name = name
  this.age = age
}
//原型上增加方法
Person.prototype.running = function() {
  console.log(`${this.name} running!!!`)
}

//子类
function Student(name, age) {
  //借用构造函数
  Person.apply(this, [name, age])
}

//调用inherit，实现继承
inherit(Student, Person)

//子类原型上的方法
Student.prototype.studying = function() {
  console.log(`${this.name} studying!!!`)
}

//Student的实例对象
const s1 = new Student('ice', 22)

console.log(s1)
s1.studying()
s1.running()

//Student { name: 'ice', age: 22 }
//ice studying!!!
//ice running!!!
```

**总结**

- 我们绕了一大圈，其实就是通过寄生的方式（中间人模式）

    - 让子类实例的隐式原型，指向了一个新的对象

        - 即 `子类实例.__proto__ = 子类.prototype = newObject`

            - 我们在原型上添加的方法，本质上是添加在这个新对象上的
        
    - 这个新的对象的隐式原型指向了父类的显示原型

        - 即 `newObject.__proto__ = 父类.prototype`

            - 这个新对象上的隐式原型(`__proto__`)，就指向了父类的prototype，从而实现了继承

- 最早的大聪明做法，我们直接把父类的`prototype`赋值给了子类的`prototype`, 它们公用同一个原型，看上去是能实现，但是它们互相影响，因为这么做意味着以后修改了子类型原型对象的某个引用类型的时候, 父类型原生对象的引用类型也会被修改。

## 5. 原型继承关系

- 这是非常经典的一张图，好像已经被分析烂了，但是很多文章感觉分析的不够透彻，可以把这张图一共分为两部分
- 前面我们已经说过了，函数比较特殊有两种身份，第一种是函数身份，另外一种是对象身份。你是一个对象，你就会有你自己的类，这样说会比较抽象，我们来一起分析，解析之前我们先把图放在旁边，一点一点的查阅。

![b58117e53b0147e79115fad4fa0a99d8_tplv-k3u1fbpfcp-zoom-in-crop-mark_3024_0_0_0.webp](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/prototype_6.webp?)

### 5.1 new Foo()

首先就是`new Foo`出发

1. `f1 = new Foo()`
     即 `f1.__proto__ === Foo.prototype`
2. `Foo.prototype`也是一个对象，也有自己的隐式原型
     即 `Foo.prototype.__proto__ === Object.prototype`
     `Object.prototype`是顶层（"尽头"）, 在继续查找就为null
3. `Foo`函数的`prototype`则是，`Foo.prototype`
4. `Foo`身为对象身份也有属于自己的`__proto__`,也有自己的构造函数
     即`Foo.__proto__ === Function.prototype `, 因为`Foo的创建`相当于 `new Function()`
5. `Function.prototype`也是一个对象，它的`__proto__`为`Object.prototype`“尽头”，在查找下去为null
6. `Function`也是一个对象，它也有自己的构造函数，但是它也是相当于`new Function`创建出来的**比较特殊**
     即`Function.__proto__ === Function.prototype `

### 5.2 new Object()

从 `new Object()`出发

1. `o1 = new Object()`
     即`o1.__proto__ === Object.prototype`, 因为它是由`Object`的实例，“尽头”，在沿着__proto__查找，则为null
2. `Object`它的显示原型为`Object.prototype`,作为对象身份也有自己的`__proto__`，那它是由谁构建出来的呢？
     即 `Object.__proto__ === Function.prototype`,它是由函数构建出来的，也可以说它是函数的实例对象

### 5.3 内存图分析

![image.png](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/prototype_7.webp?)

## 结语
>&emsp;  今天ES5继承的分享就到这边啦，明天分享ES6继承相关内容~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.08 |
