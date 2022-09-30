##	<font color=DarkBlue>大多数前端工程师不了解的promise/async await《一》</font>

## <font color=SlateBlue>前言</font>

``` 
今天给大家分享大多数前端工程师不了解的promise。promise出现解决了什么问题、异步回调地狱的终极方案并且实现async await的核心语法，其实async/await只是generator+promise的一个变种而已。
```

## <font color=SlateBlue>1. 早期异步代码困境</font>

* 众所周知，js是单线程的，耗时操作都是交给浏览器来处理，等时间到了从队列中取出执行，设计到事件循环的概念，笔者也分享过，可以看以下，理解了可以更好的理解`promise`。

* 我以一个需求为切入点，我模拟网络请求(异步操作)
  * 如果网络请求成功了，你告知我成功了
  * 如果网络请求失败了，你告知我失败了

### <font color=DeepSkyBlue>1.1 大聪明做法</font>

``` js
function requestData(url) {
  setTimeout(() => {
    if (url === 'iceweb.io') {
      return '请求成功'
    }
    return '请求失败'
  }, 3000)
}

const result = requestData('iceweb.io')
console.log(result) //undefined
```

* 首先你要理解`js`代码的执行顺序，而不是是想当然的，代码其实并不是按照你书写的顺序执行的。

* 那么为什么是 `undefined呢`？
  * 首先当我执行`requestData`函数，开始执行函数。遇到了异步操作不会阻塞后面代码执行的，因为js是单线程的，所以你写的`return`成功或者失败并没有返回，那我这个函数中，抛开异步操作，里面并没有返回值，所以值为`undefined`。

### <font color=DeepSkyBlue>1.2 早期正确做法</font>

``` js
function requestData(url, successCB, failureCB) {
  setTimeout(() => {
    if (url === 'iceweb.io') {
      successCB('我成功了,把获取到的数据传出去', [{name:'ice', age:22}])
    } else {
      failureCB('url错误，请求失败')
    }
  }, 3000)
}

//3s后 回调successCB 
//我成功了,把获取到的数据传出去 [ { name: 'ice', age: 22 } ]
requestData('iceweb.io', (res, data) => console.log(res, data), rej => console.log(rej))

//3s后回调failureCB
//url错误，请求失败
requestData('icexxx.io', res => console.log(res) ,rej => console.log(rej))
```

* 早期解决方案都是传入两个回调，一个失败的，一个成功的。那很多开发者会问这不是挺好的吗？挺简单的，js中函数是一等公民，可以传来传去，但是这样太灵活了，没有规范。
* 如果使用框架，还要阅读一下框架源码，正确失败的传实参的顺序，如果传参顺序错误这样是非常危险的。

## <font color=SlateBlue>2. Promise</font>

- `Promise`(承诺)，给予调用者一个承诺，过一会返回数据给你，就可以创建一个promise对象

- 当我们`new`一个`promise`，此时我们需要传递一个回调函数，这个函数为立即执行的，称之为（executor）这个回调函数，我们需要传入两个参数回调函数，`reslove`,`reject`(函数可以进行传参)

  - 当执行了`reslove`函数，会回调promise对象的.then函数

  - 当执行了`reject`函数，会回调promise对象的.catche函数

### <font color=DeepSkyBlue>2.1 Executor立即执行</font>

``` js
new Promise((resolve, reject) => {
  console.log(`executor 立即执行`)
})
```

* 传入的`executor`是立即执行的

### <font color=DeepSkyBlue>2.2 requestData 重构</font>

``` js
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url === 'iceweb.io') {
        //只能传递一个参数
        resolve('我成功了,把获取到的数据传出去')
      } else {
        reject('url错误，请求失败')
      }
    }, 3000)    
  })
}

//1. 请求成功
requestData('iceweb.io').then(res => {
  //我成功了,把获取到的数据传出去
  console.log(res)
})

//2. 请求失败

//2.2 第一种写法
//url错误，请求失败
requestData('iceweb.org').then(res => {},rej => console.log(rej))

//2.2 第二种写法
//url错误，请求失败
requestData('iceweb.org').catch(e => console.log(e))
```

- 在函数中，new这个类的时候，传入的回调函数称之为`executor`（会被Promise类中自动执行）

- 在正确的时候调用`resolve`函数，失败的时候调用`reject`函数，把需要的参数传递出去。

- 异常处理

  - 其中在`.then`方法中可以传入两个回调

    - 第一个则是`fulfilled`的回调

    - 第二个则是`rejected`的回调

- 那这样有什么好处呢？ 看起来比早期处理的方案还要繁琐呢?

  - 统一规范，可以增强阅读性和扩展性

  - 小幅度减少回调地狱

### <font color=DeepSkyBlue>2.3 promise的状态</font>

* 首先先给大家举个栗子，把代码抽象为现实的栗子
  - 你答应你女朋友，下周末带她去吃好吃的 (还未到下周末，此时状态为**待定状态**)
  - 时间飞快，今天就是周末了，你和你女友一起吃了烤肉、甜点、奶茶...（**已兑现状态**）
  - 时间飞快，今天就是周末了，正打算出门。不巧产品经理，因为线上出现的紧急问题，需要回公司解决一下，你(为了生活)只能委婉的拒绝一下女友，并且说明一下缘由(**已拒绝状态**)
* 使用`promise`的时候，给它一个承诺，我们可以将他划分为三个阶段
  - pending(待定)，执行了executor，状态还在等待中，没有被兑现，也没有被拒绝
  - fulfilled(已兑现)，执行了`resolve`函数则代表了已兑现状态
  - rejected(已拒绝)，执行了`reject`函数则代表了已拒绝状态
* 首先，状态只要从待定状态，变为其他状态，则状态不能再改变

思考以下代码:

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('失败')
    resolve('成功')
  }, 3000);
})

promise.then(res => console.log(res)).catch(err => console.log(err))//失败 
```

- 当我调用`reject`之后，在调用`resolve`是无效的，因为状态已经发生改变，并且是不可逆的。

###	<font color=DeepSkyBlue>2.4 resolve不同值的区别</font>

- 如果`resolve`传入一个普通的值或者对象，**只能传递接受一个参数**，那么这个值会作为`then`回调的参数

```js
const promise = new Promise((resolve, reject) => {
  resolve({name: 'ice', age: 22})
})

promise.then(res => console.log(res))// {name: 'ice', age: 22}
```

- 如果`resolve`中传入的是另外一个`Promise`，那么这个新`Promise`会决定原`Promise`的状态

```js
const promise = new Promise((resolve, reject) => {
  resolve(new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('ice')
    }, 3000);
  }))
})

promise.then(res => console.log(res))//3s后 ice
```

- 如果`resolve`中传入的是一个对象，并且这个对象有实现`then`方法，那么会执行该`then`方法，`then`方法会传入`resolve`，`reject`函数。此时的`promise`状态取决于你调用了`resolve`，还是`reject`函数。这种模式也称之为: **thenable**

```js
const promise = new Promise((resolve, reject) => {
  resolve({
    then(res, rej) {
      res('hi ice')
    }
  })
})

promise.then(res => console.log(res))// hi ice
```

###	<font color=DeepSkyBlue>2.5 Promise的实例方法</font>

* 实例方法，存放在`Promise.prototype`上的方法，也就是Promise的显示原型上，当我new Promise的时候，会把返回的改对象的 promise[[prototype]]（隐式原型） === Promise.prototype (显示原型)
* 即new返回的对象的隐式原型指向了Promise的显示原型

####		<font color=DeepSkyBlue>2.5.1 then方法</font>

#####	      	<font color=DeepSkyBlue>2.5.1.1 then的参数</font>

* `then`方法可以接受参数，一个参数为成功的回调，另一个参数为失败的回调，前面重构`requestData`中有演练过。

```js
const promise = new Promise((resolve, reject) => {
  resolve('request success')
  // reject('request error')
})

promise.then(res => console.log(res), rej => console.log(rej))

//request success
```

* 如果只捕获错误，还可以这样写
  * 因为第二个参数是捕获异常的，第一个可以写个`null`或`""`占位

```js
const promise = new Promise((resolve, reject) => {
  // resolve('request success')
  reject('request error')
})

promise.then(null, rej => console.log(rej))

//request error
```

#####	      	<font color=DeepSkyBlue>2.5.1.2 then的多次调用</font>

```js
const promise = new Promise((resolve, reject) => {
  resolve('hi ice')
})

promise.then(res => console.log(res))
promise.then(res => console.log(res))
promise.then(res => console.log(res))
```

- 调用多次则会执行多次

#####	      	<font color=DeepSkyBlue>2.5.1.3 then的返回值</font>

- `then`方法是有返回值的，它的返回值是`promise`，但是是`promise`那它的状态如何决定呢？接下来让我们一探究竟。

##### 	<font color=DeepSkyBlue>2.5.1.3.1 返回一个普通值  ***状态:fulfilled***</font>

```js
const promise = new Promise((resolve, reject) => {
  resolve('hi ice')
})

promise.then(res => ({name:'ice', age:22}))
       .then(res => console.log(res))
       
//{name:'ice', age:22}
```

- 返回一个普通值，则相当于主动调用`Promise.resolve`，并且把返回值作为实参传递到`then`方法中。

- 如果没有返回值，则相当于返回`undefined`

###### <font color=DeepSkyBlue>2.5.1.3.2 明确返回一个promise ***状态:fulfilled***</font>

```javascript
const promise = new Promise((resolve, reject) => {
  resolve('hi ice')
})

promise.then(res => {
  return new Promise((resolve, reject) => {
    resolve('then 的返回值')
  })
}).then(res => console.log(res))

//then 的返回值
```

- 主动返回一个`promise`对象，状态和你调用`resolve`，还是`reject`有关

###### <font color=DeepSkyBlue>2.5.1.3.3 **返回一个thenable对象** ***状态：fulfilled***</font>

```javascript
const promise = new Promise((resolve, reject) => {
  resolve('hi ice')
})

promise.then(res => {
  return {
    then(resolve, reject) {
      resolve('hi webice')
    }
  }
}).then(res => console.log(res))

//hi webice
```

- 返回了一个thenable对象，其状态取决于你是调用了`resolve`,还是`reject`

####	<font color=DeepSkyBlue>2.5.2 catch方法</font>

#####	<font color=DeepSkyBlue>2.5.2.1 catch的多次调用</font>

```js
const promise = new Promise((resolve, reject) => {
  reject('ice error')
})

promise.catch(err => console.log(err))
promise.catch(err => console.log(err))
promise.catch(err => console.log(err))

//ice error
//ice error
//ice error
```

#####	<font color=DeepSkyBlue>2.5.2.2 catch的返回值</font>

- catch方法是有返回值的，它的返回值是promise，但是是promise那它的状态如何决定呢？接下来让我们一探究竟。
- 如果返回值明确一个promise或者thenble对象，取决于你调用了`resolve`还是`reject`

###### <font color=DeepSkyBlue>2.5.2.2.1 返回一个普通对象</font>

```javascript
const promise = new Promise((resolve, reject) => {
  reject('ice error')
})

promise.catch(err => ({name:'ice', age: 22})).then(res => console.log(res))

//{name:'ice', age: 22}
```

###### <font color=DeepSkyBlue>2.5.2.2.2 明确返回一个promise</font>

```javascript
const promise = new Promise((resolve, reject) => {
  reject('ice error')
})

promise.catch(err => {
  return new Promise((resolve, reject) => {
    reject('ice error promise')
  })
}).catch(res => console.log(res))

//ice error promise
```

- 此时`new Promise() `调用了`reject`函数，则会被`catch`捕获到

###### <font color=DeepSkyBlue>2.5.2.2.3 返回thenble对象</font>

```javascript
const promise = new Promise((resolve, reject) => {
  reject('ice error')
})

promise.catch(err => {
  return {
    then(resolve, reject) {
      reject('ice error then')
    }
  }
}).catch(res => console.log(res))

//ice error then
```

#### <font color=DeepSkyBlue>2.5.3 finally方法</font>

- ES9（2018）新实例方法
- finally(最后)，无论promise状态是fulfilled还是rejected都会执行一次`finally`方法

```javascript
const promise = new Promise((resolve, reject) => {
  resolve('hi ice')
})

promise.then(res => console.log(res)).finally(() => console.log('finally execute'))

//finally execute
```

### <font color=DeepSkyBlue>2.6 Promise中的类方法/静态方法</font>

#### <font color=DeepSkyBlue>2.6.1 Promise.reslove</font>

```javascript
Promise.resolve('ice')
//等价于
new Promise((resolve, reject) => resolve('ice'))
```

- 有的时候，你已经预知了状态的结果为fulfilled，则可以用这种简写方式

#### <font color=DeepSkyBlue>2.6.2 Promise.reject</font>

```javascript
Promise.reject('ice error')
//等价于
new Promise((resolve, reject) => reject('ice error'))
```

- 有的时候，你已经预知了状态的结果为rejected，则可以用这种简写方式

#### 2.6.3 <font color=DeepSkyBlue>Promise.all</font>

**fulfilled 状态**

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi ice')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi panda')
  }, 2000);
})

const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi grizzly')
  }, 3000);
})


Promise.all([promise1, promise2, promise3]).then(res => console.log(res))

//[ 'hi ice', 'hi panda', 'hi grizzly' ]
```

- all方法的参数传入为一个可迭代对象，返回一个promise，只有三个都为`resolve`状态的时候才会调用`.then`方法。
- 只要有一个promise的状态为rejected，则会回调`.catch`方法

**rejected状态**

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi ice')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi panda')
  }, 2000);
})

const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi grizzly')
  }, 3000);
})

Promise.all([promise1, promise2, promise3]).then(res => console.log(res)).catch(err => console.log(err))

//hi panda
```

- 当遇到rejectd的时候，后续的promise结果我们是获取不到，并且会把reject的实参，传递给catch的err形参中

#### <font color=DeepSkyBlue>2.6.4 Promise.allSettled</font>

- 上面的`Promise.all`有一个缺陷，就是当遇到一个rejected的状态，那么对于后面是`resolve`或者`reject`的结果我们是拿不到的
- ES11 新增语法`Promise.allSettled`，无论状态是fulfilled/rejected都会把参数返回给我们

------

**所有promise都有结果**

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi ice')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi panda')
  }, 2000);
})

const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi grizzly')
  }, 3000);
})

Promise.allSettled([promise1, promise2, promise3]).then(res => console.log(res))

/* [{ status: 'rejected', reason: 'hi ice' },{ status: 'fulfilled', value: 'hi panda' },{ status: 'rejected', reason: 'hi grizzly' }] */
```

- 该方法会在所有的Promise都有结果，无论是fulfilled，还是rejected，才会有最终的结果

**其中一个promise没有结果**

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi ice')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi panda')
  }, 2000);
})

const promise3 = new Promise((resolve, reject) => {})


Promise.allSettled([promise1, promise2, promise3]).then(res => console.log(res))
// 什么都不打印
```

- 其中一个promise没有结果，则什么都结果都拿不到

#### <font color=DeepSkyBlue>2.6.5 Promise.race</font>

- race(竞争竞赛)
- 优先获取第一个返回的结果，无论结果是fulfilled还是rejectd

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi error')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi panda')
  }, 2000);
})


Promise.race([promise1, promise2])
       .then(res => console.log(res))
       .catch(e => console.log(e))
       
//hi error
```

#### <font color=DeepSkyBlue>2.6.6 Promise.any</font>

- 与race类似，只获取第一个状态为fulfilled，如果全部为rejected则报错`AggregateError`

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('hi error')
  }, 1000);
})

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('hi panda')
  }, 2000);
})


Promise.any([promise1, promise2])
       .then(res => console.log(res))
       .catch(e => console.log(e))
       
//hi panda
```



# <font color=GoldEnrod>作者</font>

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.09.30 |





