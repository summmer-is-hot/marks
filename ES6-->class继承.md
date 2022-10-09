##	<font color=SlateBlue>大多数前端工程师不了解的ES6 Class继承</font>

## 前文再续，接上一回  ES5 继承

## 6. 面向对象ES6  -> 继承

- 我们前面介绍了，通过构造函数的形式来模仿“类”，在ES6的新标准中，使用`class`关键字来定义类
- 其class的本质就是前面构造函数、原型链的那一套东西，就是几个API调用而已

### 6.1 定义类

```arduino
//1. 类申明
class Person1 {}

//2. 类表达式
const Person2 = class {}
```

### 6.2 类与构造函数

- 其本质是跟构造函数一样

```javascript
class Person {}

const p1 = new Person()

console.log(Person.prototype)
console.log(Person.prototype.constructor)
console.log(p1.__proto__ === Person.prototype)

//{}
//[class Person]
//true
```

### 6.3 类的构造函数

- 如果我们希望在创建对象的时候给类传递一些参数，这个时候应该如何做呢？
    - 每个类都可以有一个自己的构造函数（方法），这个方法的名称是固定的constructor；
    - 当我们通过new操作符，操作一个类的时候会调用这个类的构造函数constructor；
    - 每个类只能有一个构造函数，如果包含多个构造函数，那么会抛出异常；

```javascript
class Person {
  constructor(name, age, money) {
    this.name = name
    this.age = age
    this.money = money
  }

  running() {
    console.log(`${this.name} running!!!`)
  }
  eating() {
    console.log(`${this.name} eating!!!`)
  }
}

const p1 = new Person('ice', 22, 100)
console.log(p1)
p1.running()
p1.eating()

// Person { name: 'ice', age: 22, money: 100 }
// ice running!!!
// ice eating!!!
```

### 6.4 类的实例方法

```javascript
class Person {
  running() {
    console.log('running!!!')
  }
  eating() {
    console.log('eating!!!')
  }
}

const p1 = new Person()

p1.running()
p1.eating()

console.log(p1.running === Person.prototype.running)
console.log(p1.eating === Person.prototype.eating)

// running!!!
// eating!!!
// true
// true
```

- 从打印结果我们可以看出，通过类关键字定义的方法，是存放在Person的原型对象上的，这样的话可以给多个实例进行共享

### 6.5 类的静态方法(类方法)

- 静态方法通常用于定义直接使用类来执行的方法，不需要有类的实例，使用static关键字来定义：

```javascript
Function.prototype.money = 100
class Person {
  static getMoney() {
    console.log(this.money)
  }
}

Person.getMoney() //100
```

- 这里给大家留两个问题
    - 第一：静态方法中的this指向谁?（this指向这个类本身，即Class Person{}）
    - 第二： Person作为对象身份，它的隐式原型是谁? (指向`Function`的`prototype`)

### 6.6 类实现继承

```scala
class Person {}

class Student extends Person {}
```

- 对你没有看错，两行代码就实现了以前ES5以前的一系列繁琐的操作，很容易实现了继承。
- 在ES6中新增了使用extends关键字，可以方便的帮助我们实现继承
- 即 `Sub extend Super`

### 6.7 类继承初体验

```javascript
class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  }

  running() {
    console.log('running!!!')
  }
}

class Student extends Person {
  constructor(name, age, sno) {
    super(name, age)
    this.sno = sno
  }

  studying() {
    console.log('studying!!!')
  }
}

const s1 = new Student('ice', 22, 200010)

console.log(s1)
s1.running()
s1.studying()

// Student { name: 'ice', age: 22, sno: 200010 }
// running!!!
// studying!!!
```

- 我们会发现在上面的代码中我使用了一个super关键字，这个super关键字有不同的使用方式：
- 注意：在子（派生）类的构造函数中使用this或者返回默认对象之前，必须先通过super调用父类的构造函数！
- super的使用位置有三个：子类的构造函数、实例方法、静态方法

```js
class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  }

  running() {
    console.log('person running!!!')
  }
}


class Student extends Person {
  stuRunning() {
    //调用父类的方法
    super.running()
  }
}

const s1 = new Student()
s1.stuRunning()

//person running!!!
```

- 静态方法调用就不演示了，想演练的可以自己尝试一下，都比较简单就是API调用而已。

### 6.8 继承内置类

```js
class ICEArray extends Array {
  lastItem() {
    return this[this.length - 1]
  }
}

const arr = new ICEArray(1, 2, 3)
console.log(arr.lastItem()) //3
```

- 继承字内置Array类，调用`lastItem`，每次获取最后一个元素。

## 结语
>&emsp;  今天ES6 Class继承的分享就到这边了，原型、继承系列就分享完啦。期待我们的下次见面~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.09 |
