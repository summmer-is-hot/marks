# <font color=DarkBlue>彻底搞懂浏Node中的事件循环</font>

## 前文再续，接上一回

> &emsp; 昨天给大家介绍了，[什么是浏览器的事件循环](https://github.com/summmer-is-hot/marks/blob/main/eventLoop.md, 这回给大家介绍浏览器中的事件循环以及常见的面试题。

## 1.Node的事件循环

浏览器中的EventLoop是根据HTML5定义的规范来实现的，不同的浏览器可能会有不同的实现，而Node中是由libuv实现的。 下面我们来查看Node的架构图:

- 我们会发现libuv中主要维护了一个EventLoop和workerthreads
- EventLoop主要是调用一些文件IO、Network等
- libuv是一个多平台的专注于异步IO的库，它最初是为Node开发的，但是现在也被使用到Luvit、Julia、pyuv等其他地方

![nodejsEventLoop_1.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/nodejsEventLoop_1.webp?)

## 2.Node事件循环的阶段

`事件循环就像是一个桥梁，连接着应用程序的JavaScript和系统调用之间的通道`

- 无论是我们对文件的IO操作、数据库操作，都会有对应的结果和回调函数放到事件循环队列中
- 事件循环会不断从任务队列中取出对应的回调函数然后进行执行。 一次完整的事件循环可以称之为一次Tick(时钟的滴答类似) 分为多个阶段:

1. 定时器(Timers):本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数。
2. 待定回调(pading callback):对某些操作系统（如Tcp）的执行回调
3. idle，perpase:仅系统内部使用
4. 轮询(Poll):探索检测新的IO事件、执行IO相关的回调
5. setImmediate()回调函数在这里执行
6. 关闭的回调函数：一些关闭的回调函数，如：socket.on('close', ...)。

## 3.Node事件循环的阶段图解

![nodejsEventLoop_2.jpg](https://raw.githubusercontent.com/summmer-is-hot/picture-store/main/marks/nodejsEventLoop_2.webp)

## 4.Node的宏任务和微任务

- 我们会发现从一次事件循环的Tick来说，Node的事件循环更复杂，它也分为微任务和宏任务：
- 宏任务（macrotask）：setTimeout、setInterval、IO事件、setImmediate、close事件；
- 微任务（microtask）：Promise的then回调、process.nextTick、queueMicrotask； 但是，Node中的事件循环不只是 微任务队列和 宏任务队列：
- 微任务队列：

1. `next tick queue：process.nextTick；`
2. `other queue：Promise的then回调、queueMicrotask；`

- 宏任务队列:

1. `timer queue：setTimeout、setInterval`
2. ` poll queue：IO事件；`
3. `check queue：setImmediate；`
4. `close queue：close事件；`

## 5. Node事件循环的顺序

所以，在每一次事件循环的tick中，会按照如下顺序来执行代码：

next tick microtask queue other microtask queue timer queue poll queue check queue close queue

## 6. 面试题

```js
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
    console.log("setTimeout0");
}, 0);

setTimeout(function () {
    console.log("setTimeout2");
}, 300);

setImmediate(() => console.log("setImmediate"));

process.nextTick(() => console.log("nextTick1"));

async1();

process.nextTick(() => console.log("nextTick2"));

new Promise(function (resolve) {
    console.log("promise1");
    resolve();
    console.log("promise2");
}).then(function () {
    console.log("promise3");
});

console.log("script end");

//script start
//async1 start
// async2 promise1 promise2 scriptend nextTick1 nextTick2
// async1 end promise3 settimeout0 setImmediate settimeout2

```

## 结语

最后浏览器与Node中的事件循环就介绍到这里了，期待我们的下次见面~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.06 |

