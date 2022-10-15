## <font color=SlateBlue>前端常见面试题(JS篇)五</font>

## 五、异步与事件循环

## 5.1. 异步编程的实现方式?

JavaScript中的异步机制可以分为以下几种：

- **回调函数** 的方式，使用回调函数的方式有一个缺点是，多个回调函数嵌套的时候会造成回调函数地狱，上下两层的回调函数间的代码耦合度太高，不利于代码的可维护。
- **Promise** 的方式，使用 Promise 的方式可以将嵌套的回调函数作为链式调用。但是使用这种方法，有时会造成多个 then 的链式调用，可能会造成代码的语义不够明确。
- **generator** 的方式，它可以在函数的执行过程中，将函数的执行权转移出去，在函数外部还可以将执行权转移回来。当遇到异步函数执行的时候，将函数执行权转移出去，当异步函数执行完毕时再将执行权给转移回来。因此在 generator
  内部对于异步操作的方式，可以以同步的顺序来书写。使用这种方式需要考虑的问题是何时将函数的控制权转移回来，因此需要有一个自动执行 generator 的机制，比如说 co 模块等方式来实现 generator 的自动执行。
- **async 函数** 的方式，async 函数是 generator 和 promise 实现的一个自动执行的语法糖，它内部自带执行器，当函数内部执行到一个 await 语句的时候，如果语句返回一个 promise
  对象，那么函数将会等待 promise 对象的状态变为 resolve 后再继续向下执行。因此可以将异步逻辑，转化为同步的顺序来书写，并且这个函数可以自动执行。

## 5.2 并发与并行的区别？

- 并发是宏观概念，我分别有任务 A 和任务 B，在一段时间内通过任务间的切换完成了这两个任务，这种情况就可以称之为并发。
- 并行是微观概念，假设 CPU 中存在两个核心，那么我就可以同时完成任务 A、B。同时完成多个任务的情况就可以称之为并行。

## 5.3 setTimeout、setInterval、requestAnimationFrame的区别

- **setTimeout**

执行该语句时，是立即把当前定时器代码推入事件队列，当定时器在事件列表中满足设置的时间值时将传入的函数加入任务队列，之后的执行就交给任务队列负责。但是如果此时任务队列不为空，则需等待，所以执行定时器内代码的时间可能会大于设置的时间。

返回值`timeoutID`
是一个正整数，表示定时器的编号。这个值可以传递给`clearTimeout()`
来取消该定时器。

- **setInterval**

重复调用一个函数或执行一个代码片段，每次都精确的隔一段时间推入一个事件（但是，事件的执行时间不一定就不准确，还有可能是这个事件还没执行完毕，下一个事件就来了）。它返回一个 `interval ID`，该 ID
唯一地标识时间间隔，因此你可以稍后通过调用 `clearInterval()`
来移除定时器。

**技术上，`clearTimeout()` 和 `clearInterval()`可以互换。但是，为了避免混淆，不要混用取消定时函数。**

- **requestAnimationFrame**

是JS实现动画的一种方式，它告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

## 5.4. 什么是回调地狱？回调地狱会带来什么问题？

回调函数的层层嵌套，就叫做回调地狱。回调地狱会造成代码可复用性不强，可阅读性差，可维护性(迭代性差)，扩展性差等等问题。

## Promise语法

## 5.5. Promise是什么

Promise是异步编程的一种解决方案，它是一个对象，可以获取异步操作的消息，他的出现大大改善了异步编程的困境，避免了地狱回调，它比传统的解决方案回调函数和事件更合理和更强大。

**promise本身只是一个容器,真正异步的是它的两个回调resolve()和reject()**

**promise本质 不是控制 异步代码的执行顺序（无法控制） ， 而是控制异步代码结果处理的顺序**

## 5.6 promise实例有哪些状态，怎么改变状态

（1）Promise的实例有**三个状态**:

- Pending（进行中）
- Resolved（已完成）
- Rejected（已拒绝）

当把一件事情交给promise时，它的状态就是Pending，任务完成了状态就变成了Resolved、没有完成失败了就变成了Rejected。

**如何改变 promise 的状态**

- resolve(value): 如果当前是 pending 就会变为 resolved
- reject(error): 如果当前是 pending 就会变为 rejected
- 抛出异常: 如果当前是 pending 就会变为 rejected

注意：一旦从进行状态变成为其他状态就永远不能更改状态了。

## 5.7 创建Promise实例有哪些方法

- **new Promise((resolve,reject)=>{** ... **})**

**一般情况下都会使用**`new Promise()`**来创建promise对象，但是也可以使用**`promise.resolve`**和**`promise.reject`**这两个方法：**

- **Promise.resolve**

`Promise.resolve(value)`的返回值也是一个promise对象，可以对返回值进行.then调用，代码如下：

```javascript
Promise.resolve(11).then(function (value) {
    console.log(value); // 打印出11
});
```

- **Promise.reject**

`Promise.reject` 也是`new Promise`的快捷形式，也创建一个promise对象。代码如下：

```javascript
Promise.reject(new Error("出错了！！"))
```

## 5.8 Promise有哪些实例方法

**then**

`then`方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为`resolved`时调用，第二个回调函数是Promise对象的状态变为`rejected`时调用。其中第二个参数可以省略。 `then`方法返回的是一个新的Promise实例（不是原来那个Promise实例）。因此可以采用链式写法，即`then`方法后面再调用另一个then方法。

**catch**

该方法相当于`then`方法的第二个参数，指向`reject`的回调函数。不过`catch`方法还有一个作用，就是在执行`resolve`回调函数时，如果出现错误，抛出异常，不会停止运行，而是进入`catch`方法中。

**finally**

`finally`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

下面是一个例子，服务器使用 Promise 处理请求，然后使用`finally`方法关掉服务器。

```js
server.listen(port)
    .then(function () {
        // ...
    })
    .finally(server.stop);
```

`finally`方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是`fulfilled`还是`rejected`。这表明，`finally`方法里面的操作，应该是与状态无关的，不依赖于 Promise的执行结果。

## 5.9 Promise有哪些静态方法

**all**

`all`方法可以完成并发任务， 它接收一个数组，数组的每一项都是一个`promise`
对象，返回一个[`Promise`](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FPromise)
实例。当数组中所有的`promise`的状态都达到`resolved`的时候，`all`方法的状态就会变成`resolved`，如果有一个状态变成了`rejected`，那么`all`方法的状态就会变成`rejected`。

**race**

`race`方法和`all`一样，接受的参数是一个每项都是`promise`的数组，但是与`all`不同的是，当最先执行完的事件执行完之后，就直接返回该`promise`对象的值。如果第一个`promise`对象状态变成`resolved`
，那自身的状态变成了`resolved`；反之第一个`promise`变成`rejected`，那自身状态就会变成`rejected`。

**any**

它接收一个数组，数组的每一项都是一个`promise`对象，该方

------

法会返回一个新的 `promise`，数组内的任意一个 `promise` 变成了`resolved`状态，那么由该方法所返回的 `promise`
就会变成`resolved`状态。如果数组内的 `promise` 状态都是`rejected`，那么该方法所返回的 `promise` 就会变成`rejected`状态，

**resolve、reject**

用来生成对应状态的Promise实例

## 5.10 Promise.all、Promise.race、Promise.any的区别

**all：** 成功的时候返回的是**一个结果数组**，而失败的时候则返回**最先被reject失败状态的值**。

**race：** 哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。

**any：** 返回最快的成功结果，如果全部失败就返回失败结果。

## 5.11 一个promise指定多个回调函数, 都会调用吗?

都会调用，成功状态放在then的第一个参数里调用

```javascript
let p2 = new Promise((resolve, reject) => {
    resolve(1)
})
p2.then(value => {
    console.log('第一个', value)
})
p2.then(value => {
    console.log('第二个', value)
})
```

失败状态放在then的第二个参数里调用

```javascript
let p3 = new Promise((resolve, reject) => {
    reject(2)
})
p3.then(
    () => {
    },
    value => {
        console.log('第一个', value)
    }
)
p3.then(
    () => {
    },
    value => {
        console.log('第二个', value)
    }
)
```

## 5.12 改变 promise 状态和指定回调函数谁先谁后?

1. 都有可能, 正常情况下是先指定回调再改变状态, 但也可以先改状态再指定回调
2. 如何先改状态再指定回调?
    - 在执行器中直接调用 resolve()/reject()
    - 延迟更长时间才调用 then()
3. 什么时候才能得到数据?
    - 如果先指定的回调, 那当状态发生改变时, 回调函数就会调用, 得到数据
    - 如果先改变的状态, 那当指定回调时, 回调函数就会调用, 得到数据

## 5.13 promise.then()返回的新 promise 的结果状态由什么决定?

1. 简单表达: 由 then()指定的回调函数执行的结果决定
2. 详细表达:
    - 如果抛出异常, 新 promise 变为 rejected, 参数为抛出的异常
    - 如果返回的是非 promise 的任意值, 新 promise 变为 resolved, value 为返回的值
    - 如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果

## 5.14 promise 如何串连多个操作任务?

- promise 的 then()返回一个新的 promise, 可以开成 then()的链式调用
- 通过 then 的链式调用串连多个同步/异步任务

## 5.15 promise 异常传透是什么?

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获。

- 当使用 promise 的 then 链式调用时, 可以在最后指定失败的回调,
- 前面任何操作出了异常, 都会传到最后失败的回调中处理

## 5.16 如何中断 promise 链?

- 当使用 promise 的 then 链式调用时, 在中间中断, 不再调用后面的回调函数。 在回调函数中返回一个 `pendding` 状态的 promise 对象

## 5.17 promise有什么缺点

**代码层面**

- 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
- 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

**语法层面**

- Promise虽然摆脱了回调地狱，但是then的链式调⽤也会带来额外的阅读负担
- Promise传递中间值⾮常麻烦
- Promise的调试很差，由于没有代码块，你不能在⼀个返回表达式的箭头函数中设置断点，如果你在⼀个.then代码块中使⽤调试器的步进(step-over)功能，调试器并不会进⼊后续的.then代码块，因为调试器只能跟踪同步代码的每⼀步。

## async/await语法

## 5.18 async 函数是什么

- 一句话概括： 它就是 Generator 函数的语法糖，也就是处理异步操作的另一种`高级写法`

## 5.19 async 函数的实现原理

async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。

```javascript
async function fn(args) {
    // ...
}

// 等同于

function fn(args) {
    return spawn(function* () {  // spawn函数就是自动执行器
        // ...
    });
}
```

## 5.20 async函数的返回值

`async`函数返回一个 Promise 对象。

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。

## 5.21 await 到底在等待什么?

await 等待的是一个表达式，这个表达式的计算结果是 Promise 对象或者其它值（换句话说，就是没有特殊限定）。await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await
后面实际是可以接普通函数调用或者直接量的。

await 表达式的运算结果取决于它等的是什么。

- 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
- 如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

## 5.22 什么是顶层await？

从 ES2022 开始，允许在模块的顶层独立使用`await`命令，使得上面那行代码不会报错了。它的主要目的是使用`await`**解决模块异步加载的问题。**

```javascript
import {AsyncFun} from 'module'

await AsyncFun()
console.log(123)
```

## 5.23 如何用await让程序停顿指定的时间(休眠效果)

JavaScript 一直没有休眠的语法，但是借助`await`命令就可以让程序停顿指定的时间

```javascript
function sleep(interval) {
    return new Promise(resolve => {
        setTimeout(resolve, interval);
    })
}

// 用法
async function one2FiveInAsync() {
    for (let i = 1; i <= 5; i++) {
        console.log(i);
        await sleep(1000);
    }
}

one2FiveInAsync();
```

## 5.24 await的使用注意点

1. `await`命令后面的`Promise`对象，运行结果可能是`rejected`，所以最好把`await`命令放在`try...catch`代码块中。
2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
3. `await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。
4. async 函数可以保留运行堆栈。

## 5.25 async语法怎么捕获异常

`async`函数内部的异常可以通过 `.catch()`或者 `try`/`catch`来捕获,区别是

- try/catch 能捕获所有异常,try语句抛出错误后会执行catch语句，try语句内后面的内容不会执行
- catch（）只能捕获异步方法中reject错误，并且catch语句之后的语句会继续执行

```javascript
//async函数错误捕获，以登录功能为例
async function getCatch() {
    await new Promise(function (resolve, reject) {
        reject(new Error('登录失败'))
    }).catch(error => {
        console.log(error)  // .catch（）能捕获到错误信息
    })
    console.log('登录成功') //  但是成功信息也会执行
}

async function getCatch() {
    try {
        await new Promise(function (resolve, reject) {
            reject(new Error('登录失败'))
        })
        console.log('登录成功')  // try抛出错误之后，就不会执行这条语句
    } catch (error) {
        console.log(error)  //  catch语句能捕获到错误信息
    }
}
```

## 5.26 async/await对比Promise的优势

- 代码读起来更加同步，Promise虽然摆脱了回调地狱，但是then的链式调⽤也会带来额外的阅读负担
- Promise传递中间值⾮常麻烦，⽽async/await⼏乎是同步的写法，⾮常优雅
- 错误处理友好，async/await可以⽤成熟的try/catch，Promise的错误捕获⾮常冗余
- 调试友好，Promise的调试很差，由于没有代码块，你不能在⼀个返回表达式的箭头函数中设置断点，如果你在⼀个.then代码块中使⽤调试器的步进(step-over)
  功能，调试器并不会进⼊后续的.then代码块，因为调试器只能跟踪同步代码的每⼀步。

## 事件循环Event Loop

## 5.27 JS的执行机制(同步任务、异步任务)

JS是一门单线程语言，单线程就意味着，所有的任务需要排队，前一个任务结束，才会执行下一个任务。这样所导致的问题是：如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞的觉。为了解决这个问题，JS中出现了同步和异步。

**同步任务**：即主线程上的任务，按照顺序由上⾄下依次执⾏，当前⼀个任务执⾏完毕后，才能执⾏下⼀个任务。

**异步任务**：不进⼊主线程，⽽是进⼊任务队列的任务，执行完毕之后会产生一个回调函数,并且通知主线程。当主线程上的任务执行完后，就会调取最早通知自己的回调函数，使其进入主线程中执行。

## 5.28 什么是Event Loop

- 事件循环Event Loop又叫事件队列，两者是一个概念

事件循环指的是js代码所在运行环境（浏览器、nodejs）编译器的一种解析执行规则。事件循环不属于js代码本身的范畴，而是属于js编译器的范畴，在js中讨论事件循环是没有意义的。换句话说，js代码可以理解为是一个人在公司中具体做的事情， 而
事件循环 相当于是公司的一种规章制度。 两者不是一个层面的概念。

## 5.29 宏任务与微任务的概念与区别

为了协调任务有条不紊地在主线程上执行，页面进程引入了 **消息队列** 和 **事件循环机制**，渲染进程内部也会维护多个消息队列，比如延迟执行队列和普通的消息队列。然后主线程采用一个 `for`
循环，不断地从这些任务队列中取出任务并执行任务。这些消息队列中的任务就称为 **宏任务**。

**微任务**是一个需要异步执行的回调函数，执行时机是在主函数执行结束之后、当前宏任务结束之前。当 JS 执行一段脚本（一个宏任务）的时候，V8 会为其创建一个全局执行上下文，在创建全局执行上下文的同时，V8 引擎也会在内部创建一个 **
微任务队列**。也就是说 **每个宏任务都关联了一个微任务队列**。

## 5.30 常见的宏任务与微任务分别有哪些

| 任务（代码）           | 宏/微 任务 | 环境        |
| ---------------------- | ---------- | ----------- |
|                        | 宏任务     | 浏览器      |
| 事件                   | 宏任务     | 浏览器      |
| 网络请求（Ajax）       | 宏任务     | 浏览器      |
| setTimeout() 定时器    | 宏任务     | 浏览器/Node |
| fs.readFile() 读取文件 | 宏任务     | Node        |
| Promise.then()         | 微任务     | 浏览器/Node |
| async/await            | 微任务     | 浏览器/Node |

## 5.31 事件循环Event Loop执行机制

1.进入到script标签,就进入到了第一次事件循环.

2.遇到同步代码，立即执行

3.遇到宏任务,放入到宏任务队列里.

4.遇到微任务,放入到微任务队列里.

5.执行完所有同步代码

6.执行微任务代码

7.微任务代码执行完毕，本次队列清空

8.寻找下一个宏任务，重复步骤1

## 5.32 为什么Js是单线程？

Js是单线程，但是浏览器是多线程。单线程是为了避免UI操作混乱，所有和UI操作相关的开发语言都应该是单线程。

## 5.33 代码题易考点

1. promise本身是一个同步的代码，只有它后面调用的then()方法里面的回调才是微任务
2. then方法需要Promise里的resolve传值才会执行
3. await右边的表达式还是会立即执行,表达式之后的代码才是微任务, await微任务可以转换成等价的promise微任务分析
4. script标签本身是一个`宏任务`， 当页面出现多个script标签的时候，浏览器会把script标签作为宏任务来解析

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.15 |
