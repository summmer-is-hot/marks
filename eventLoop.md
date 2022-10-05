## <font color=DarkBlue>彻底搞懂浏览器 Event Loop 事件循环</font>

## 前言

>   浏览器的事件循环和Node的事件循环是每个前端工程师必须掌握的知识点，我们平常最多的就是跟浏览器打交道。本文先介绍浏览器的事件循环，笔者从以下几个角度去分析问题从而引出到底什么是事件循环？

1. 你们了解操作系统中 什么是进程？ 什么是线程？
2. JS是单线程还是多线程的？
3. 浏览器是多进程还是单进程？ 为什么？
4. 如果JS是单线程的，它是如何实现异步处理的？
5. 引出事件循环

## 1.什么是进程？ 什么是线程？

&emsp;   首先给大家推荐一本《现代操作系统》这本书，真正的理解了操作系统的本质，许多开发问题都会豁然开朗。`在操作系统中无论是运行系统中的什么应用，最终都是会编译成二进制交CPU给执行。` </br>
&emsp;   我们在日常使用电脑中，我们可能会一边听歌、一边写代码、一边写博客。这些应用中都会在操作系统中开启一个进程或多个进程，不同的应用开启的进程是不一样的。为了大家好理解就比喻某个应用中只会开启一个进程，而一个进程当中会开启多个线程。如果只有一个线程的话，我们称之为主线程。`结论:一个进程当中包含多个线程或只有一个主线程。` </br>
&emsp;   **画了一幅图让大家更加直观的了解进程和线程的关系**

![eventLoop_1.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/eventLoop_1.webp?)  </br>
&emsp;   在这副图中，最外层的就相当于操作系统，我们在会在操作系统下载很多应用，当我们运行VsCode、网易云音乐、某信等...   </br>
&emsp;   他们都会开启一个进程或多个进程（这个跟应用程序本身有关），一个进程里面又包含了多个线程，如果只有一个线程称之为主线程。 </br>
&emsp;   如果当您看到了这里，还是对进程跟线程不是特别了解，请允许我用一个通俗易懂的例子来描述他们之间的关系。 </br>
&emsp;   `操作系统就跟工厂一样，一个工厂会包含多个车间，其中多个车间就好比是我们的应用程序有多个进程，车间里面有工人在工作，就相当于是线程的概念。` </br>

## 2.JS是单线程还是多线程的？

&emsp;   `答案：JS是单线程。`如果您深究为什么是单线程的呢？ </br>
&emsp;   其实这是与它的用途有关，因为JS是一门浏览器脚本语言，主要用途是进行用户操作和操作DOM，所以它只能是单线程的，否则会带来很多复杂的同步问题。

## 3.浏览器是多进程还是单进程

&emsp;   `答案：浏览器是多进程的。`为什么说是多进程的？ 你说是就是吗？ 凭什么呢？</br>
&emsp;   当我们浏览网页的时候，有的时候是不是会遇到浏览器卡死的情况。如果我们开了多个会话，就假如我们一边刷力扣，一边开发程序，写循环的时候，写了一个死循环，导致了我们开发的这个会话的崩溃，如果浏览器是单进程的情况下，力扣这个时候也会崩溃。 </br>
&emsp;   当然浏览器肯定不会允许这样的事情发生，它就是多进程的，多个会话互相不影响，你要崩溃你崩溃去，跟我可没关系~~~。

## 4.JS实现异步处理

&emsp;   首先当您看到了这里，其实你们离成功只有一步之遥了，跟着我继续探讨下去，把Event Loop一点一点捣碎了，喂进你们的嘴里。</br>

&emsp;   `我们先来看一段同步的JS代码`

```js
const foo = 'foo'

function bar() {
  console.log('bar')
}

console.log(foo) //foo
bar() //bar

```

&emsp;   JS的代码执行顺序是`从上至下`进行的，所以答案如注释所示，我们是毫无疑问的

&emsp;   现在玩点花样，来一点同步和异步代码给大家看看

```js
const foo = 'foo'

function bar() {
  console.log('bar')
}

queueMicrotask(() => {
  console.log('microtask')
})

console.log(foo) 

setTimeout(() => {
  console.log('setTimeout')
},1000)

bar()

//主线程: foo bar
//微任务队列:microtask
//宏任务队列:setTimeout
//执行顺序: foo bar microtask setTimeout(1s过后)

```

&emsp;  现在穿插了异步的代码，如果把上面从上至下运行代码的结论用到异步，肯定是错误的！ 正确的执行顺序的答案，大家可以先看一下，可能对异步不太了解的人会产生疑惑，不要慌，我们一点一点来看，引出下面的事件循环。

## 5.事件循环

&emsp;  首先我们又回到 JS 代码是`单线程`开始讲起，当我们编写的`同步`的代码的时候，代码的执行顺序是从上至下的，但是当有`异步`操作或者一些耗时操作的时候，如果还是按照之前的结论从上至下的话，那么一定会堵塞我们主线程的代码 也就是 main script中的代码，这样在js当中肯定是不被允许的。假如我有耗时操作（setTimeout）或者网络请求（ajax），延迟了5s才执行，那我后面的代码都`堵塞`了。  
&emsp;  用文字的形式，还是有点抽象，我们直接上图，到底是一个怎么样的机制，完美解决这一问题。 

![eventLoop_2.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/eventLoop_2.webp?) 
    简单描述一下这幅图:首先js代码先执行主线程的代码，也就是同步的代码，从上至下，遇到异步代码交给浏览器，浏览器专门开了一个线程，其中浏览器线程中维护这两个队列，`一个微任务队列，一个宏任务队列`。

`宏任务队列 Macrotask Queue: ajax、setTimeout、setInterval、Dom监听等 `  ;

`微任务队列 Microtask Queue: Promise的then回调、 Mutation Observer API、queueMicrotask `;

`注意:每一次执行宏任务之前，都是要确保我微任务的队列是空的，也就是说从代码执行的顺序来说微任务优先于宏任务。`

但是存在插队的情况，也就是说当我微任务执行完了，要开始执行宏任务了（有多个宏任务），宏任务队列当队列中的代码执行了，宏任务队列里面又有微任务代码，又把微任务放入到微任务队列当中。

此时特别注意！！！从严格的意义来说，紧接着是先进行编译的宏任务，但是此时微任务里面有任务才去执行的微任务队列，而不是直接去执行的。这些异步的代码交给js执行，`这样三者形成了一个闭环，我们称之为事件循环`。

## 6.常见面试题

### &emsp;1.面试题一

看到下面的代码，直接copy到编辑器当中，跟我来分析一波。学会了什么是事件循环？

```js
setTimeout(function () {
  console.log("setTimeout1");
  new Promise(function (resolve) {
    resolve();
  }).then(function () {
    new Promise(function (resolve) {
      resolve();
    }).then(function () {
      console.log("then4");
    });
    console.log("then2");
  });
});

new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("then1");
});

setTimeout(function () {
  console.log("setTimeout2");
});

console.log(2);

queueMicrotask(() => {
  console.log("queueMicrotask1");
});

new Promise(function (resolve) {
  resolve();
}).then(function () {
  console.log("then3");
});

//promise1 2 then1 queueMicrotask1 then3 setTimeout1 then2 then4 setTimeout2
```

&emsp;  请耐心观看 下面执行顺序图（你想要的都在图里）：

![eventLoop_3.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/eventLoop_3.webp?)

### &emsp; 2.面试题二

&emsp;  我们穿插一道简单的吧，首先您要对async await 有一定的了解，如果您不太了解，下方评论，我会尽快更新的，直接告诉您`结论:async函数当中，await后面的代码相当于promise.then的代码，是一个微任务，但是await所在的函数可是会直接执行的，不能混淆了。` 话不多说，我们直接上代码。

```js
async function bar() {
  console.log("222222");
  return new Promise((resolve, reject) => {
    reject();
  });
}

async function foo() {
  console.log("111111");
  await bar().catch((err) => {});
  console.log("333333");
}
foo();
console.log("444444");

//111111 222222 444444 333333

```

&emsp;  执行顺序分析图:

![eventLoop_4.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/eventLoop_4.webp?)

### &emsp; 3.面试题三

&emsp;  `这是一道字节的面试题，让我们来给它撕烂吧`，废话少说直接上代码

```js
//字节面试题
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");

setTimeout(function () {
  console.log("setTimeout");
}, 0);

async1();

new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2");
});

console.log("script end");

/*
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
*/

```

&emsp;  请仔细查看分析图:

![eventLoop_5.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/eventLoop_5.webp?)

## 结语

看完本期分享，浏览器的事件循环告一段落了，基本可以手撕90%以上的面试题~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.05 |
