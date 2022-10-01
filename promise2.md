##	<font color=DarkBlue>大多数前端工程师不了解的promise/async await《二》</font>

## <font color=SlateBlue>3. Promise的回调地狱 (进阶)</font>

- 我还是以一个需求作为切入点，把知识点嚼碎了，一点一点喂进你们嘴里。
    - 当我发送网络请求的时候，需要拿到这次网络请求的数据，再发送网络请求，就这样重复三次，才能拿到我最终的结果。

### <font color=DeepSkyBlue>3.1 卧龙解法</font>

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}


requestData('iceweb.io').then(res => {
  requestData(`iceweb.org ${res}`).then(res => {
    requestData(`iceweb.com ${res}`).then(res => {
      console.log(res)
    })
  })
})
//iceweb.com iceweb.org iceweb.io
```

- 虽然能够实现，但是多层代码的嵌套，可读性非常差，我们把这种多层次代码嵌套称之为回调地狱

### <font color=DeepSkyBlue>3.2 凤雏解法</font>

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}

requestData('iceweb.io').then(res => {
  return requestData(`iceweb.org ${res}`)
}).then(res => {
  return requestData(`iceweb.com ${res}`)
}).then(res => {
  console.log(res)
})
//iceweb.com iceweb.org iceweb.io
```

- 利用了then链式调用这一特性，返回了一个新的promise，但是不够优雅，思考一下能不能写成同步的方式呢？

### <font color=DeepSkyBlue>3.3 生成器+Promise解法</font>

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}

function* getData(url) {
  const res1 = yield requestData(url)
  const res2 = yield requestData(res1)
  const res3 = yield requestData(res2)

  console.log(res3)
}

const generator = getData('iceweb.io')

generator.next().value.then(res1 => {
  generator.next(`iceweb.org ${res1}`).value.then(res2 => {
    generator.next(`iceweb.com ${res2}`).value.then(res3 => {
      generator.next(res3)
    })
  })
})

//iceweb.com iceweb.org iceweb.io
```

- 大家可以发现我们的`getData`已经变为同步的形式，可以拿到我最终的结果了。那么很多同学会问，generator一直调用`.next`不是也产生了回调地狱吗？
- 其实不用关心这个，我们可以发现它这个是有规律的，我们可以封装成一个自动化执行的函数，我们就不用关心内部是如何调用的了。

### <font color=DeepSkyBlue>3.4 自动化执行函数封装</font>

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}

function* getData() {
  const res1 = yield requestData('iceweb.io')
  const res2 = yield requestData(`iceweb.org ${res1}`)
  const res3 = yield requestData(`iceweb.com ${res2}`)

  console.log(res3)
}

//自动化执行 async await相当于自动帮我们执行.next
function asyncAutomation(genFn) {
  const generator = genFn()

  const _automation = (result) => {
    let nextData = generator.next(result)
    if(nextData.done) return

    nextData.value.then(res => {
      _automation(res)
    })
  }

  _automation()
}

asyncAutomation(getData)

//iceweb.com iceweb.org iceweb.io
```

- 利用promise+生成器的方式变相实现解决回调地狱问题，其实就是`async await`的一个变种而已
- 最早为**TJ**实现，**前端大神人物**
- async await核心代码就类似这些，内部主动帮我们调用`.next`方法

### <font color=DeepSkyBlue>3.5 最终解决回调地狱的办法</font>

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}

async function getData() {
  const res1 = await requestData('iceweb.io')
  const res2 = await requestData(`iceweb.org ${res1}`)
  const res3 = await requestData(`iceweb.com ${res2}`)

  console.log(res3)
}

getData()

//iceweb.com iceweb.org iceweb.io
```

- 你会惊奇的发现，只要把`getData`生成器函数函数，改为`async`函数，`yeild`的关键字替换为`await`就可以实现异步代码同步写法了。

## <font color=SlateBlue>4. async/await 剖析</font>

- async（异步的）
- async 用于申明一个异步函数

### <font color=DeepSkyBlue>4.1 async内部代码同步执行</font>

- 异步函数的内部代码执行过程和普通的函数是一致的，默认情况下也是会被同步执行

```javascript
async function sayHi() {
  console.log('hi ice')
}

sayHi()

//hi ice
```

### <font color=DeepSkyBlue>4.2 异步函数的返回值</font>

- 异步函数的返回值和普通返回值有所区别
    - 普通函数主动返回什么就返回什么，不返回为`undefined`
    - 异步函数的返回值特点
        - 明确有返回一个普通值，相当于`Promise.resolve`(返回值)
        - 返回一个thenble对象则由，then方法中的`resolve`,或者`reject`有关
        - 明确返回一个promise，则由这个promise决定
- 异步函数中可以使用`await`关键字，现在在全局也可以进行`await`，但是不推荐。会阻塞主进程的代码执行

### <font color=DeepSkyBlue>4.3 异步函数的异常处理</font>

- 如果函数内部中途发生错误，可以通过try catch的方式捕获异常
- 如果函数内部中途发生错误，也可以通过函数的返回值.catch进行捕获

```js
async function sayHi() {
  console.log(res)
}
sayHi().catch(e => console.log(e))

//或者

async function sayHi() {
  try {
    console.log(res)
  }catch(e) {
    console.log(e)
  }
}

sayHi()

//ReferenceError: res is not defined
```

### <font color=DeepSkyBlue>4.4 await 关键字</font>

- 异步函数中可以使用`await`关键字，普通函数不行
- await特点
    - 通常await关键字后面都是跟一个Promise
        - 可以是普通值
        - 可以是thenble
        - 可以是Promise主动调用`resolve或者reject`
    - 这个promise状态变为fulfilled才会执行`await`后续的代码，所以`await`后面的代码，相当于包括在`.then`方法的回调中，如果状态变为rejected，你则需要在函数内部`try catch`，或者进行链式调用进行`.catch`操作

```javascript
function requestData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (url.includes('iceweb')) {
        resolve(url)
      } else {
        reject('请求错误')
      }
    }, 1000);
  })
}

async function getData() {
  const res = await requestData('iceweb.io')
  console.log(res)
}

getData()
// iceweb.io
```

## <font color=SlateBlue>5. 结语</font>

- 在如今互联网寒冬下，如果现在真的看不到未来是怎样，你就不如一直往前走，不知道什么时候天亮，去奔跑就好，跑着跑着天就亮了。

# <font color=GoldEnrod>作者</font>

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.01 |

