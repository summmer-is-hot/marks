## <font color=SlateBlue>前端常见面试题(JS篇)四</font>

## 四、原型与继承

## 4.1说说面向对象的特性与特点

- 封装性
- 继承性
- 多态性

面向对象编程具有灵活、代码可复用、容易维护和开发的有点、更适合多人合作的大型软件项目

## 4.2 说说你对工厂模式的理解

工厂模式是用来创建对象的一种最常用的设计模式，不暴露创建对象的具体逻辑，而是将将逻辑封装在一个函数中，那么这个函数就可以被视为一个工厂

其就像工厂一样重复的产生类似的产品，工厂模式只需要我们传入正确的参数，就能生产类似的产品

## 4.3 创建对象有哪几种方式？

1. 字面量的形式直接创建对象
2. 函数方法
    1. **工厂模式**，工厂模式的主要工作原理是用函数来封装创建对象的细节，从而通过调用函数来达到复用的目的。
    2. **构造函数模式**
    3. **原型模式**
    4. **构造函数模式+原型模式**，这是创建自定义类型的最常见方式。
    5. **动态原型模式**
    6. **寄生构造函数模式**
3. class创建

## 4.4 JS宿主对象和原生对象的区别

```
原生对象
```

“`独立于宿主环境的 ECMAScript 实现提供的对象`”

包含：Object、Function、Array、String、Boolean、Number、Date、RegExp、Error、EvalError、RangeError、ReferenceError、SyntaxError、TypeError、URIError

```
内置对象
```

开发者不必明确实例化内置对象，它已被内部实例化了

同样是“独立于宿主环境”。而 ECMA-262 只定义了两个内置对象，即 Global 和 Math

```
宿主对象
```

BOM 和 DOM 都是宿主对象。因为其对于不同的“宿主”环境所展示的内容不同。其实说白了就是，ECMAScript 官方未定义的对象都属于宿主对象，因为其未定义的对象大多数是自己通过 ECMAScript 程序创建的对象

## 4.5 JavaScript 内置的常用对象有哪些？并列举该对象常用的方法？

**Number 数值对象，数值常用方法**

- Number.toFixed( ) 采用定点计数法格式化数字
- Number.toString( ) 将—个数字转换成字符串
- Number.valueOf( ) 返回原始数值

**String 字符串对象，字符串常用方法**

- Length 获取字符串的长度
- split()将一个字符串切割数组
- concat() 连接字符串
- indexOf()返回一个子字符串在原始字符串中的索引值。如果没有找到，则返回固定值 -1
- lastIndexOf() 从后向前检索一个字符串
- slice() 抽取一个子串

**Boolean 布尔对象，布尔常用方法**

- Boolean.toString() 将布尔值转换成字符串
- Boolean.valueOf() Boolean 对象的原始值的布尔值

**Array 数组对象，数组常用方法**

- join() 将一个数组转成字符串。返回一个字符串
- reverse() 将数组中各元素颠倒顺序
- delete 运算符只能删除数组元素的值，而所占空间还在，总长度没变(arr.length)
- shift()删除数组中第一个元素，返回删除的那个值，并将长度减 1
- pop()删除数组中最后一个元素，返回删除的那个值，并将长度减 1
- unshift() 往数组前面添加一个或多个数组元素，长度会改变
- push() 往数组结尾添加一个或多个数组元素，长度会改变
- <!--concat-->() 连接数组
- slice() 切割数组，返回数组的一部分
- splice()插入、删除或替换数组的元素
- toLocaleString() 把数组转换成局部字符串
- toString()将数组转换成一个字符串
- forEach()遍历所有元素
- every()判断所有元素是否都符合条件
- sort()对数组元素进行排序
- map()对元素重新组装，生成新数组
- filter()过滤符合条件的元素
- find() 查找 返回满足提供的测试函数的第一个元素的值。否则返回 undefined。
- some() 判断是否有一个满足条件 ，返回布尔值
- fill() 填充数组
- flat() 数组扁平化

**Function 函数对象，函数常用方法**

- Function.arguments 传递给函数的参数
- Function.apply() 将函数作为一个对象的方法调用
- Function.call() 将函数作为对象的方法调用
- Function.caller 调用当前函数的函数
- Function.length 已声明的参数的个数
- Function.prototype 对象类的原型
- Function.toString() 把函数转换成字符串

**Object 基础对象，对象常用方法**

- Object 含有所有 JavaScript 对象的特性的超类
- Object.constructor 对象的构造函数
- Object.hasOwnProperty( ) 检查属性是否被继承
- Object.isPrototypeOf( ) 一个对象是否是另一个对象的原型
- Object.propertyIsEnumerable( ) 是否可以通过 for/in 循环看到属性
- Object.toLocaleString( ) 返回对象的本地字符串表示
- Object.toString( ) 定义一个对象的字符串表示
- Object.valueOf( ) 指定对象的原始值

**Date 日期时间对象，日期常用方法**

- Date.getFullYear() 返回 Date 对象的年份字段
- Date.getMonth() 返回 Date 对象的月份字段
- Date.getDate() 返回一个月中的某一天
- Date.getDay() 返回一周中的某一天
- Date.getHours() 返回 Date 对象的小时字段
- Date.getMinutes() 返回 Date 对象的分钟字段
- Date.getSeconds() 返回 Date 对象的秒字段
- Date.getMilliseconds() 返回 Date 对象的毫秒字段
- Date.getTime() 返回 Date 对象的毫秒表示

**Math 数学对象，数学常用方法**

- Math 对象是一个`静态对象`
- Math.PI 圆周率
- Math.abs() 绝对值
- Math.ceil() 向上取整(整数加 1，小数去掉)
- Math.floor() 向下取整(直接去掉小数)
- Math.round() 四舍五入
- Math.pow(x，y) 求 x 的 y 次方
- Math.sqrt() 求平方根

**RegExp 正则表达式对象，正则常用方法**

- RegExp.exec() 检索字符串中指定的值。返回找到的值，并确定其位置。
- RegExp.test( ) 检索字符串中指定的值。返回 true 或 false。
- RegExp.toString( ) 把正则表达式转换成字符串
- RegExp.global 判断是否设置了 "g" 修饰符
- RegExp.ignoreCase 判断是否设置了 "i" 修饰符
- RegExp.lastIndex 用于规定下次匹配的起始位置
- RegExp.source 返回正则表达式的匹配模式

**Error 异常对象**

- Error.message 设置或返回一个错误信息(字符串)
- Error.name 设置或返回一个错误名
- Error.toString( ) 把 Error 对象转换成字符串

## 4.6 说一下hasOwnProperty、instanceof方法

**hasOwnProperty()** 方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是，是否有指定的键）。

**instanceof** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。

## 4.7 什么是原型对象，说说对它的理解

**构造函数的内部的 prototype 属性指向的对象，就是构造函数的原型对象。**

原型对象包含了可以由该构造函数的所有实例共享的属性和方法。当使用构造函数新建一个实例对象后，在这个对象的内部将包含一个指针(***\*proto\****)，这个指针指向构造函数的 原型对象，在 ES5 中这个指针被称为对象的原型。

## 4.8 什么是原型链

**原型链是一种查找规则**

当访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，这种链式查找过程称之为原型链

## 4.9 原型链的终点是什么？

原型链的尽头是null。也就是`Object.prototype.__proto__`

## 4.10 Js实现继承的方法

### 1.原型链继承

**关键：子类构造函数的原型为父类构造函数的实例对象**

**缺点**：1、子类构造函数无法向父类构造函数传参。

　　　2、所有的子类实例共享着一个原型对象，一旦原型对象的属性发生改变，所有子类的实例对象都会收影响

　　　3、如果要给子类的原型上添加方法，必须放在Son.prototype = new Father()语句后面

```javascript
    function Father(name) {
      this.name = name
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    function Son(age) {
      this.age = 20
    }
    // 原型链继承,将子函数的原型绑定到父函数的实例上,子函数可以通过原型链查找到复函数的原型,实现继承
    Son.prototype = new Father()
    // 将Son原型的构造函数指回Son, 否则Son实例的constructor会指向Father
    Son.prototype.constructor = Son
    Son.prototype.showAge = function () {
      console.log(this.age);
    }
    let son = new Son(20, '刘逍') // 无法向父构造函数里传参
    // 子类构造函数的实例继承了父类构造函数原型的属性,所以可以访问到父类构造函数原型里的showName方法
    // 子类构造函数的实例继承了父类构造函数的属性,但是无法传参赋值,所以是this.name是undefined
    son.showName() // undefined
    son.showAge()  // 20
```

### 2.借用构造函数继承

**关键：用 .call() 和 .apply()方法,在子类构造函数中,调用父类构造函数**

**缺点**：1、只继承了父类构造函数的属性，没有继承父类原型的属性。

　　　2、无法实现函数复用，如果父类构造函数里面有一个方法，会导致每一个子类实例上面都有相同的方法。

```javascript
    function Father(name) {
      this.name = name
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    function Son(name, age) {
      Father.call(this, name) // 在Son中借用了Father函数,只继承了父类构造函数的属性，没有继承父类原型的属性。
      // 相当于 this.name = name
      this.age = age
    }
    let s = new Son('刘逍', 20) // 可以给父构造函数传参
    console.log(s.name); // '刘逍'
    console.log(s.showName); // undefined
```

### 3.组合继承

**关键：原型链继承+借用构造函数继承**

**缺点**：1、使用组合继承时，父类构造函数会被调用两次，子类实例对象与子类的原型上会有相同的方法与属性，浪费内存。

```javascript
    function Father(name) {
      this.name = name
      this.say = function () {
        console.log('hello,world');
      }
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    function Son(name, age) {
      Father.call(this, name) //借用构造函数继承
      this.age = age
    }
    // 原型链继承
    Son.prototype = new Father()  // Son实例的原型上,会有同样的属性,父类构造函数相当于调用了两次
    // 将Son原型的构造函数指回Son, 否则Son实例的constructor会指向Father
    Son.prototype.constructor = Son
    Son.prototype.showAge = function () {
      console.log(this.age);
    }
    let p = new Son('刘逍', 20) // 可以向父构造函数里传参
    // 也继承了父函数原型上的方法
    console.log(p);
    p.showName() // '刘逍'
    p.showAge()  // 20
```

### 4.原型式继承

**关键：创建一个函数，将要继承的对象通过参数传递给这个函数，最终返回一个对象，它的隐式原型指向传入的对象。** (***Object.create()方法的底层就是原型式继承***)

**缺点**：只能继承父类函数原型对象上的属性和方法，无法给父类构造函数传参

```javascript
    function createObj(obj) {
      function F() { }   // 声明一个构造函数
      F.prototype = obj   //将这个构造函数的原型指向传入的对象
      F.prototype.construct = F   // construct属性指回子类构造函数
      return new F        // 返回子类构造函数的实例
    }
    function Father() {
      this.name = '刘逍'
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    const son = createObj(Father.prototype)
    son.showName() // undefined  继承了原型上的方法,但是没有继承构造函数里的name属性
```

### 5.寄生式继承

**关键：在原型式继承的函数里，给继承的对象上添加属性和方法，增强这个对象**

**缺点**：只能继承父类函数原型对象上的属性和方法，无法给父类构造函数传参

```javascript
    function createObj(obj) {
      function F() { }
      F.prototype = obj
      F.prototype.construct = F
      F.prototype.age = 20  // 给F函数的原型添加属性和方法,增强对象
      F.prototype.showAge = function () {
        console.log(this.age);
      }
      return new F
    }
    function Father() {
      this.name = '刘逍'
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    const son = createObj(Father.prototype)
    son.showName() // undefined
    son.showAge()  // 20
```

### 6.寄生组合继承

**关键：原型式继承 + 构造函数继承**

**Js最佳的继承方式，只调用了一次父类构造函数**

```javascript
    function Father(name) {
      this.name = name
      this.say = function () {
        console.log('hello,world');
      }
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }
    function Son(name, age) {
      Father.call(this, name)
      this.age = age
    }
    Son.prototype = Object.create(Father.prototype) // Object.create方法返回一个对象，它的隐式原型指向传入的对象。
    Son.prototype.constructor = Son
    const son = new Son('刘逍', 20)
    console.log(son.prototype.name); // 原型上已经没有name属性了,所以这里会报错
```

### 7.混入继承

**关键：利用Object.assign的方法多个父类函数的原型拷贝给子类原型**

```javascript
  function Father(name) {
      this.name = name
    }
    Father.prototype.showName = function () {
      console.log(this.name);
    }

    function Mather(color) {
      this.color = color
    }
    Mather.prototype.showColor = function () {
      console.log(this.color);
    }

    function Son(name, color, age) {
      // 调用两个父类函数
      Father.call(this, name)
      Mather.call(this, color)
      this.age = age
    }
    Son.prototype = Object.create(Father.prototype)
    Object.assign(Son.prototype, Mather.prototype)  // 将Mather父类函数的原型拷贝给子类函数
    const son = new Son('刘逍', 'red', 20)
    son.showColor()  // red
```

### 8. class继承

**关键：class里的extends和super关键字，继承效果与寄生组合继承一样**

```javascript
    class Father {
      constructor(name) {
        this.name = name
      }
      showName() {
        console.log(this.name);
      }
    }
    class Son extends Father {  // 子类通过extends继承父类
      constructor(name, age) {
        super(name)    // 调用父类里的constructor函数,等同于Father.call(this,name)
        this.age = age
      }
      showAge() {
        console.log(this.age);
      }
    }
    const son = new Son('刘逍', 20)
    son.showName()  // '刘逍'
    son.showAge()   // 20
```
## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.14 |
