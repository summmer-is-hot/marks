## <font color=SlateBlue>前端常见面试题(浏览器篇)一</font>

## 一、HTTP

## 1. GET和POST的请求的区别

Post 和 Get 是 HTTP 请求的两种方法，其区别如下：

- **应用场景：** (GET 请求是一个**幂等**的请求)一般 Get 请求用于对服务器资源不会产生影响的场景，比如说请求一个网页的资源。(而 Post 不是一个**幂等**的请求)
  一般用于对服务器资源会产生影响的情景，比如注册用户这一类的操作。（**幂等是指一个请求方法执行多次和仅执行一次的效果完全相同**）
- **是否缓存：** 因为两者应用场景不同，浏览器一般会对 Get 请求缓存，但很少对 Post 请求缓存。
- **传参方式不同：** Get 通过查询字符串传参，Post 通过请求体传参。
- **安全性：** Get 请求可以将请求的参数放入 url 中向服务器发送，这样的做法相对于 Post 请求来说是不太安全的，因为请求的 url 会被保留在历史记录中。
- **请求长度：** 浏览器由于对 url 长度的限制，所以会影响 get 请求发送数据时的长度。这个限制是浏览器规定的，并不是 RFC 规定的。
- **参数类型：** get参数只允许ASCII字符，post 的参数传递支持更多的数据类型(如文件、图片)。

## 2. POST和PUT请求的区别

PUT请求是向服务器端发送数据，从而修改数据的内容，但是不会增加数据的种类等，也就是说无论进行多少次PUT操作，其结果并没有不同。（可以理解为时**更新数据**）

POST请求是向服务器端发送数据，该请求会改变数据的种类等资源，它会创建新的内容。（可以理解为是**创建数据**）

### 为什么post请求会发送两次请求?

- 1.第一次请求为`options`预检请求，状态码为:204
    - 作用：
        - 作用1: 询问服务器是否支持修改的请求头，如果服务器支持，则在第二次中发送真正的请求
        - 作用2: 检测服务器是否为同源请求,是否支持跨域
- 2.第二次为真正的`post`请求

## 3. 常见的HTTP请求头和响应头

### HTTP Request Header

- Accept:浏览器能够处理的内容类型
- Accept-Charset:浏览器能够显示的字符集
- Accept-Encoding：浏览器能够处理的压缩编码
- Accept-Language：浏览器当前设置的语言
- Connection：浏览器与服务器之间连接的类型
- Cookie：当前页面设置的任何Cookie
- Host：发出请求的页面所在的域
- Referer：发出请求的页面的URL
- User-Agent：浏览器的用户代理字符串

### HTTP Responses Header

- Date：表示消息发送的时间，时间的描述格式由rfc822定义
- server:服务器名称
- Connection：浏览器与服务器之间连接的类型
- Cache-Control：控制HTTP缓存
- content-type:表示后面的文档属于什么MIME类型

### Content-Type

**常见的 Content-Type 属性值有以下四种：**

（1）`application/x-www-form-urlencoded`：浏览器的原生 form 表单，如果不设置 enctype 属性，那么最终就会以 application/x-www-form-urlencoded
方式提交数据。该种方式提交的数据放在 body 里面，数据按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL转码。

（2）`multipart/form-data`：该种方式也是一个常见的 POST 提交方式，通常表单上传文件时使用该种方式。

（3）`application/json`：服务器消息主体是序列化后的 JSON 字符串。

（4）`text/xml`：该种方式主要用来提交 XML 格式的数据。

## 4. HTTP状态码304是多好还是少好

**为什么会有304**

服务器为了提高网站访问速度，对之前访问的部分页面指定缓存机制，当客户端在此对这些页面进行请求，服务器会根据缓存内容判断页面与之前是否相同，若相同便直接返回304，此时客户端调用缓存内容，不必进行二次下载。

状态码304不应该认为是一种错误，而是对客户端**有缓存情况下**服务端的一种响应。

搜索引擎蜘蛛会更加青睐内容源更新频繁的网站。通过特定时间内对网站抓取返回的状态码来调节对该网站的抓取频次。若网站在一定时间内一直处于304的状态，那么蜘蛛可能会降低对网站的抓取次数。相反，若网站变化的频率非常之快，每次抓取都能获取新内容，那么日积月累，的回访率也会提高。

**产生较多304状态码的原因：**

- 页面更新周期长或不更新
- 纯静态页面或强制生成静态html

**304状态码出现过多会造成以下问题：**

- 网站快照停止；
- 收录减少；
- 权重下降。

## 5. 常见的HTTP请求方法

- `GET`: 向服务器获取数据；
- `POST`：发送数据给服务器，通常会造成服务器资源的新增修改；
- `PUT`：用于全量修改目标资源(看接口，也可以用于添加)；
- `PATCH`：用于对资源进行部分修改
- `DELETE`：用于删除指定的资源；
- `HEAD`：获取报文首部，与GET相比，不返回报文主体部分；使用场景是比如下载一个大文件前，先获取其大小再决定是否要下载，以此可以节约宽带资源
- `OPTIONS`：(浏览器自动执行)、询问支持的请求方法，用来跨域请求、预检请求、判断目标是否安全；
- `CONNECT`：要求在与代理服务器通信时建立**管道**，使用**管道**进行TCP通信；(把服务器作为跳板，让服务器代替用户去访问其他网页，之后把数据原原本本的返回给用户)
- `TRACE`: 该方法会让服务器原样返回任意客户端请求的信息内容，主要⽤于测试或诊断。

## 6. 说说Ajax组成部分

Ajax(阿贾克斯)：全称 `Asynchronous Javascript And XML(异步的js与xml)`

- 说人话： **用js发送异步的网络请求**
- A : Asynchronous 异步
- J：Javascript
- A ：And
- X : XML 与 XMLHttpRequest
    - XML ： 解决跨平台数据传输。
        - 在JSON没有出来以前, 网络传输主要以XML格式数据为主。 后来JSON问世，逐渐取代XML。 但是由于ajax技术出来的比json早，因此xml这个称呼一直保留至今

## 7. 请介绍一下XMLHTTPRequest对象

**Ajax的核心是XMLHTTPRequest**。它是一种支持异步请求的技术。 XMLHTTPRequest使您可以使用JavaScript向服务器提出请求并处理响应，而不阻塞用户。可以在页面加载以后进行页面的局部更新

**使用方法**

**1.实例化ajax对象**

**2. open()** ：创建HTTP请求 第一个参数是指定提交方式(post、get) 第二个参数是指定要提交的地址是哪 第三个参数是指定是异步还是同步(true表示异步，false表示同步)
第四和第五参数在HTTP认证的时候会用到。是可选的

**3.设置请求头**

**setRequestHeader**(StringHeader,StringValue) **（使用post方式才会使用到，get方法并不需要调用该方法）**

**4.发送请求**

**send(content)** ：发送请求给服务器 如果是get方式，并不需要填写参数，或填写null 如果是post方式，把要提交的参数写上去

**5. 注册回调函数**

```javascript
 /* 1.ajax： 在页面不刷新的情况下向服务器请求数据
           2.XMLHttpRequest ： http请求对象，负责实现ajax技术（小黄人）
                （1）创建XMLHttpRequest对象
                        * 小黄人，相当于黄袍加身的跑腿外卖小哥哥
                （2）设置请求
                        * 告诉小黄人服务器地址
                （3）发送请求
                        * 小黄人出发去指定地址取外卖（数据）
                            * 2G网速：走路去的
                            * 3G网速：骑膜拜去的
                            * WIFI : 骑电动车去的
                            * 4G   ： 骑小牛牌电动车去的
                （4）注册回调函数
                        * 小黄人把取回的外卖送到你家门口
        
         */
//(1).实例化ajax对象
let xhr = new XMLHttpRequest()
//(2).设置请求方法和地址
xhr.open("post", "http://www.liulongbin.top:3009/api/login")
//(3).设置请求头（post请求才需要设置）
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
//(4).发送请求 ： 参数格式  'key=value'
xhr.send("username=admin&password=123456")
//(5).注册回调函数
// xhr.onload = function() {};
xhr.onreadystatechange = function () {
    //onreadystatechange会触发多次，一般需要判断xhr.readState == 4 才获取响应数据
    if (xhr.readyState == 4) {
        console.log(xhr.responseText)
    }
}
```

### 7.2. onreadStateChange事件

```js
//      1. onload事件 ：  接收服务器响应的数（一次请求，只会执行一次）
//      2. onreadystatechang事件 : 作用与onload事件一致（一次请求，会执行多次）
//          面试点： XMLHttpRequest对象的状态码 （xhr.readyState）
//             0: 请求未建立  (创建了xhr对象，但是还没调用open)
//             1: 服务器连接已建立 
//             2. 请求已接收  (send之后,服务器已经接收了请求)
//             3. 请求处理中 
//             4. 请求已完成，且响应已就绪 （ 4状态码等同于onload事件 ）


//(1).实例化ajax对象
let xhr = new XMLHttpRequest()
console.log(xhr.readyState) //0
//(2).设置请求方法和地址
xhr.open("post", "http://www.liulongbin.top:3009/api/login")
console.log(xhr.readyState) //1
//(3).设置请求头（post请求才需要设置）
xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
console.log(xhr.readyState) //1
//(4).发送请求 ： 参数格式  'key=value'
xhr.send("username=admin&password=123456")
console.log(xhr.readyState) //1
//(5).注册回调函数
//a. onload 是新式浏览器才支持的
//b. 如果要兼容更早的浏览器，可以使用 onreadystatechange
//c. onreadystatechange触发时机 ： xhr.readState状态变化
// xhr.onload = function() {};

xhr.onreadystatechange = function () {
    console.log(xhr.readyState) //2,3,4
    //onreadystatechange会触发多次，一般需要判断xhr.readState == 4 才获取响应数据
    if (xhr.readyState == 4) {
        console.log(xhr.responseText)
    }
}
```

### 7.3 如何上传文件（上传图片）

```javascript
   /*文件上传思路总结 
      1. 给file表单注册onchange事件 
        * 当用户选择图片之后执行
      2. 获取用户选择的图片 
        * this.files[0]
      3. 创建FormData对象 
        * 只有FormData才可以上传文件
      4. 将图片添加到FormData对象中 
        * fd.append('参数名', this.files[0])
      5. 发送ajax请求
        * 文件上传请求方法一定是post, 且请求参数为 FormData对象
      */

//1. file类型表单自带一个选择文件点击按钮，当用户选择文件之后就会触发onchange事件
document.querySelector("#iptFile").onchange = function () {
    //this : file表单
    //(1)获取用户选择的文件
    let file = this.files[0]
    // 非空判断，如果内容为undefined，给出提示
    if (file == undefined) {
        return alert("请选择上传文件！")
    }
    //(2)创建FormData对象， 只有FormData对象才可以上传文件
    let fd = new FormData()
    //(3)添加文件
    fd.append("avatar", file)
    //(4)发送ajax请求, 参数为 FormData对象
    axios({
        method: "POST",
        url: "http://www.liulongbin.top:3009/api/upload/avatar",
        data: fd
    }).then(({data: res}) => {
        console.log(res)
        if (res.code != 200) {
            return alert(res.message)
        }
        // 成功后提示，修改图片路径
        alert("恭喜您，上传头像成功！")
        document.querySelector("img").src = `http://www.liulongbin.top:3009${res.url}`
    })
}
```

### 7.4 如何自定义上传文件按钮

```javascript
    /*自定义文件上传按钮思路
      (1)隐藏file表单
      (2)给自定义按钮添加一个点击事件
      (3)点击按钮的时候，触发 file表单的点击 
       */
document.querySelector('#btnChoose').onclick = function () {
    /* 
    dom对象.onclick()  :  只能触发你自己注册的onclick事件，没注册触发不了
    dom对象.click() : 模拟鼠标点击。 触发注册的onclick事件 + 默认点击事件
    */
    document.querySelector('#iptFile').click()
}
```

### 7.5 ajax请求如何取消

**1. 原生xhr取消请求**

```js
var xhr = new XMLHttpRequest();
xhr.abort();
```

**2.axios取消请求**

**1.使用 CancelToken.source 工厂方法创建 cancel token**

```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
axios.get('/user/123', {
    cancelToken: source.token
}).catch(function(thrown) {
    if (axios.isCancel(thrown)) {
        console.log('Request canceled', thrown.message);
    } else {
    // 处理错误
    }
});

axios.post('/user/123', {
    name: '小明'
}, {
    cancelToken: source.token
})

// 取消请求（message 参数是可选的）
source.cancel('canceled by the user.');
```

**2.传递一个 executor 函数到 CancelToken 的构造函数来创建 cancel token**

```js
const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // executor 函数接收一个 cancel 函数作为参数
    cancel = c;
  })
});

// cancel the request
cancel();
```

### 7.6取消ajax请求有什么意义

**取消ajax请求的意义**

1. 已发出的请求可能仍然会到达后端
2. 取消后续的回调处理，避免多余的回调处理，以及特殊情况，先发出的后返回，导致回调中的数据错误覆盖
3. 取消loading效果，以及该请求的其他交互效果，特别是在单页应用中，A页面跳转到B页面之后，A页面的请求应该取消，否则回调中的一些处理可能影响B页面
4. 超时处理，错误处理等都省去了，节约资源

## 8. OPTIONS请求方法及使用场景

OPTIONS是除了GET和POST之外的其中一种 HTTP请求方法。(浏览器自动执行)

OPTIONS方法是用于请求获得由`Request-URI`标识的资源在请求/响应的通信过程中可以使用的功能选项。通过这个方法，客户端可以**在采取具体资源请求之前，决定对该资源采取何种必要措施，或者了解服务器的性能**
。该请求方法的响应不能缓存。

OPTIONS请求方法的**主要用途**有两个：

- 获取服务器支持的所有HTTP请求方法；
- 用来检查访问权限。例如：在进行 CORS 跨域资源共享时，对于复杂请求，就是使用 OPTIONS 方法发送**嗅探**请求，以判断是否有对指定资源的访问权限。

## 9. HTTP 1.0 和 HTTP 1.1 之间有哪些区别？

- **连接方面**，http1.0 默认使用非持久连接，而 http1.1 默认使用持久连接。http1.1 通过使用持久连接来使多个 http 请求复用同一个 TCP 连接，以此来避免使用非持久连接时每次需要建立连接的时延。
- **资源请求方面**，在 http1.0 中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，http1.1 则在请求头引入了 range
  头域，它允许只请求资源的某个部分，即返回码是 206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。
- **缓存方面**，在 http1.0 中主要使用 header 里的 If-Modified-Since、Expires 来做为缓存判断的标准，http1.1 则引入了更多的缓存控制策略，例如
  Etag、If-Unmodified-Since、If-Match、If-None-Match 等更多可供选择的缓存头来控制缓存策略。
- **http1.1** 中**新增了 host 字段**，用来指定服务器的域名。http1.0 中认为每台服务器都绑定一个唯一的 IP 地址，因此，请求消息中的 URL
  并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机，并且它们共享一个IP地址。因此有了 host 字段，这样就可以将请求发往到同一台服务器上的不同网站。
- http1.1 相对于 http1.0 还新增了很多**请求方法**，如 PUT、HEAD、OPTIONS 等。

## 10.HTTP 1.1 和 HTTP 2.0 的区别

- **二进制协议**：HTTP/2 是一个二进制协议。在 HTTP/1.1 版中，报文的头信息必须是文本（ASCII 编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，头信息和数据体都是二进制，并且统称为"
  帧"，可以分为头信息帧和数据帧。 帧的概念是它实现多路复用的基础。
- **多路复用：** HTTP/2 实现了多路复用，HTTP/2 仍然复用 TCP 连接，但是在一个连接里，客户端和服务器都可以同时发送多个请求或回应，而且不用按照顺序一一发送，这样就避免了"队头堵塞"【1】的问题。
- **数据流：** HTTP/2 使用了数据流的概念，因为 HTTP/2 的数据包是不按顺序发送的，同一个连接里面连续的数据包，可能属于不同的请求。因此，必须要对数据包做标记，指出它属于哪个请求。HTTP/2
  将每个请求或回应的所有数据包，称为一个数据流。每个数据流都有一个独一无二的编号。数据包发送时，都必须标记数据流 ID ，用来区分它属于哪个数据流。
- **头信息压缩：** HTTP/2 实现了头信息压缩，由于 HTTP 1.1 协议不带状态，每次请求都必须附上所有信息。所以，请求的很多字段都是重复的，比如 Cookie 和 User Agent
  ，一模一样的内容，每次请求都必须附带，这会浪费很多带宽，也影响速度。HTTP/2 对这一点做了优化，引入了头信息压缩机制。一方面，头信息使用 gzip 或 compress
  压缩后再发送；另一方面，客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送同样字段了，只发送索引号，这样就能提高速度了。
- **服务器推送：** HTTP/2 允许服务器未经请求，主动向客户端发送资源，这叫做服务器推送。使用服务器推送提前给客户端推送必要的资源，这样就可以相对减少一些延迟时间。这里需要注意的是 http2 下服务器主动推送的是静态资源，和
  WebSocket 以及使用 SSE 等方式向客户端发送即时数据的推送是不同的。

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.11.02 |
