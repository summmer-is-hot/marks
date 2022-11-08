## <font color=SlateBlue>前端常见面试题(浏览器篇)三</font>

## 二、HTTPS

## 1. 什么是HTTPS协议？

`超文本传输安全协议`（Hypertext Transfer Protocol Secure，简称：HTTPS）是一种通过计算机网络进行安全通信的传输协议。**HTTPS经由HTTP进行通信，利用SSL/TLS来加密数据包。**
HTTPS的主要目的是提供对网站服务器的身份认证，保护交换数据的隐私与完整性。 ![img](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/browser/1.webp?)
HTTP协议采用**明文传输**信息，存在**信息窃听**、**信息篡改**和**信息劫持**的风险，而协议TLS/SSL具有**身份验证**、**信息加密**和**完整性校验**的功能，可以避免此类问题发生。

安全层的主要职责就是**对发起的HTTP请求的数据进行加密操作** 和 **对接收到的HTTP的内容进行解密操作**。

## 2. TLS/SSL的工作原理

**TLS**全称**安全传输层协议**（Transport Layer Security）及其前身**安全套接层**（Secure Sockets Layer，缩写作**SSL**）
是介于TCP和HTTP之间的一层安全协议，不影响原有的TCP协议和HTTP协议，所以使用HTTPS基本上不需要对HTTP页面进行太多的改造。

TLS/SSL的功能实现主要依赖三类基本算法：**散列函数hash**、**对称加密**、**非对称加密**。这三类算法的作用如下：

- 散列算法用来验证信息的完整性
- 对称加密算法采用协商的秘钥对数据加密
- 非对称加密实现身份认证和秘钥协商

![2.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/browser/2.webp?)

## 3. 对称加密、非对称加密是什么，有什么区别？

**对称加密和非对称加密是安全传输层里的加密算法**

**对称加密**

- 对称加密的特点是文件加密和解密使用相同的密钥，即加密密钥也可以用作解密密钥，

  这种方法在密码学中叫做对称加密算法，对称加密算法使用起来简单快捷，密钥较短，且破译困难

  **通信的双⽅都使⽤同⼀个秘钥进⾏加密, 解密。** ⽐如，两个人事先约定的暗号，就属于对称加密。

![img](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/browser/3.webp?)

**优点:**

计算量小、加密速度快、加密效率高。

**缺点:**

**在数据传送前，发送方和接收方必须商定好秘钥，然后双方保存好秘钥。**

**如果一方的秘钥被泄露，那么加密信息也就不安全了**

最不安全的地方, 就在于第一开始, 互相约定密钥的时候!!! 传递密钥!

使用场景：本地数据加密、https 通信、网络传输等

**非对称加密**

通信的双方使用不同的秘钥进行加密解密，即秘钥对（私钥 + 公钥）。

特征: 私钥可以解密公钥加密的内容, 公钥可以解密私钥加密的内容

![img](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/browser/4.webp?)

非对称加密的特点是：

- 优点：非对称加密与对称加密相比其安全性更好
- **缺点：加密和解密花费时间长、速度慢，只适合对少量数据进行加密。**

使用场景：https 会话前期、CA 数字证书、信息加密、登录认证等

## 4. 数字证书是什么？

使用一种 Hash 算法来对公钥和其他信息进行加密，生成一个信息摘要，然后让有公信力的认证中心（简称 CA ）用它的私钥对消息摘要加密，形成签名。最后将原始的信息和签名合在一起，称为**数字证书**
。当接收方收到数字证书的时候，先根据原始信息使用同样的 Hash 算法生成一个摘要，然后使用公证处的公钥来对数字证书中的摘要进行解密，最后将解密的摘要和生成的摘要进行对比，就能发现得到的信息是否被更改了。

### 4.2 数字证书的作用

现在的方法也不一定是安全的，因为没有办法确定得到的公钥就一定是安全的公钥。可能存在一个中间人，截取了对方发给我们的公钥，然后将他自己的公钥发送给我们，当我们使用他的公钥加密后发送的信息，就可以被他用自己的私钥解密。然后他伪装成我们以同样的方法向对方发送信息，这样我们的信息就被窃取了，然而自己还不知道。为了解决这样的问题，可以使用**
数字证书**。

### 4.3 数字签名是什么？

数字签名就是先用CA自带的Hash算法来计算出证书内容的一个摘要，然后使用CA私钥进行加密，组成数字签名。

当别人把他的数字证书发过来时，接收方用同样的算法再次生成摘要，用CA公钥解密后得到CA生成的摘要，两者进行对比后，就能确定中间是否被人篡改。这样就能最大程度的保证通信的安全了。

## 5. HTTPS通信（握手）过程

HTTPS的通信过程如下：

1. 客户端向服务器发起请求，请求中包含使用的协议版本号、生成的一个随机数、以及客户端支持的加密方法。
2. 服务器端接收到请求后，确认双方使用的加密方法、并给出服务器的证书、以及一个服务器生成的随机数。
3. 客户端确认服务器证书有效后，生成一个新的随机数，并使用数字证书中的公钥，加密这个随机数，然后发给服 务器。并且还会提供一个前面所有内容的 hash 的值，用来供服务器检验。
4. 服务器使用自己的私钥，来解密客户端发送过来的随机数。并提供前面所有内容的 hash 值来供客户端检验。
5. 客户端和服务器端根据约定的加密方法使用前面的三个随机数，生成对话秘钥，以后的对话过程都使用这个秘钥来加密信息。

## 6. HTTPS的优缺点

HTTPS的**优点**如下：

- 使用HTTPS协议可以认证用户和服务器，确保数据发送到正确的客户端和服务器；
- 使用HTTPS协议可以进行加密传输、身份认证，通信更加安全，防止数据在传输过程中被窃取、修改，确保数据安全性；
- HTTPS是现行架构下最安全的解决方案，虽然不是绝对的安全，但是大幅增加了中间人攻击的成本；

HTTPS的**缺点**如下：

- HTTPS需要做服务器和客户端双方的加密个解密处理，耗费更多服务器资源，过程复杂；
- HTTPS协议握手阶段比较费时，增加页面的加载时间；
- SSL证书是收费的，功能越强大的证书费用越高；
- HTTPS连接服务器端资源占用高很多，支持访客稍多的网站需要投入更大的成本；
- SSL证书需要绑定IP，不能再同一个IP上绑定多个域名。

## 7. **HTTPS**是如何保证安全的？

结合**对称加密**和**非对称加密**两种加密⽅式，将对称加密的密钥使⽤⾮对称加密的公钥进⾏加密，然后发送出去，接收⽅使⽤私钥进⾏解密得到对称加密的密钥，然后双⽅可以使⽤对称加密来进⾏沟通。

这个时候还需要⼀个安全的**第三⽅颁发证书**（CA），证明身份的身份，防⽌被中间⼈攻击。

为了防止中间人篡改证书，需要用到**数字签名**这个技术

*

数字签名就是⽤CA⾃带的HASH算法对证书的内容进⾏HASH得到⼀个摘要，再⽤CA的私钥加密，最终组成数字签名。当别⼈把他的证书发过来的时候,我再⽤同样的Hash算法,再次⽣成消息摘要，然后⽤CA的公钥对数字签名解密,得到CA创建的消息摘要,两者⼀⽐,就知道中间有没有被⼈篡改了。这个时候就能最⼤程度保证通信的安全了。*

## 8.HTTP状态码分别代表什么意思？

| **类别** | **原因**                        | **描述**                   |
| -------- | ------------------------------- | -------------------------- |
| 1xx      | Informational(信息性状态码)     | 接受的请求正在处理         |
| 2xx      | Success(成功状态码)             | 请求正常处理完毕           |
| 3xx      | Redirection(重定向状态码)       | 需要进行附加操作一完成请求 |
| 4xx      | Client Error (客户端错误状态码) | 服务器无法处理请求         |
| 5xx      | Server Error(服务器错误状态码)  | 服务器处理请求出错         |

**（1）2XX 成功**

- 200 OK，表示从客户端发来的请求在服务器端被正确处理
- 201 Created 请求已经被实现，而且有一个新的资源已经依据请求的需要而建立。通常是在POST请求，或者是某些PUT请求之后创建了内容，进行的返回的响应。
- 202 Accepted 请求服务器已接受，但是尚未处理，不保证完成请求。适合异步任务或者说需要处理时间比较长的请求，避免HTTP链接一直占用。
- 204 No content，表示请求成功，但响应报文不含实体的主体部分
- 205 Reset Content，表示请求成功，但响应报文不含实体的主体部分，但是与 204 响应不同在于要求请求方重置内容
- 206 Partial Content，进行的是范围请求，表示服务器已经成功处理了部分GET请求，响应头中会包含获取的内容范围(常用于分段下载)

**（2）3XX 重定向**

- 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
- 302 found，临时性重定向，表示资源临时被分配了新的 URL，支持搜索引擎优化
- 303 see other，表示资源存在着另一个 URL，应使用 GET 方法获取资源
- 304 not modified，自从上次请求后，请求的网页内容未修改过。服务器返回此响应时，不会返回网页内容。(**协商缓存**)
- 307 temporary redirect，临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出请求

**（3）4XX 客户端错误**

- 400 bad request，请求报文存在语法错误(传参格式不正确)
- 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息(没有权限)
- 403 forbidden，表示对请求资源的访问被服务器拒绝
- 404 not found，表示在服务器上没有找到请求的资源
- 408 Request Timeout 客户端请求超时
- 409 Conflict 请求的资源可能引起冲突

**（4）5XX 服务器错误**

- 500 internal sever error，表示服务器端在执行请求时发生了错误
- 501 Not Implemented，表示服务器不支持当前请求所需要的某个功能
- 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求

## 9. 同样是重定向，**307**，**303**，**302**的区别？

**302**是http1.0的协议状态码，在http1.1版本的时候为了细化302状态码⼜出来了两个303和307。

**303**明确表示客户端应当采⽤get⽅法获取资源，他会把POST请求变为GET请求进⾏重定向。

**307**会遵照浏览器标准，不会从post变为get。

## 10. DNS 协议是什么

**概念**： DNS 是**域名系统** (Domain Name System) 的缩写，提供的是一种主机名到 IP 地址的转换服务，就是我们常说的域名系统。它是一个由分层的 DNS
服务器组成的分布式数据库，是定义了主机如何查询这个分布式数据库的方式的应用层协议。能够使人更方便的访问互联网，而不用去记住能够被机器直接读取的IP数串。

**作用**： 将域名解析为IP地址，客户端向DNS服务器（DNS服务器有自己的IP地址）发送域名查询请求，DNS服务器告知客户机Web服务器的 IP 地址。

## 11. DNS完整的查询过程

DNS服务器解析域名的过程：

- 首先会在**浏览器的缓存**中查找对应的IP地址，如果查找到直接返回，若找不到继续下一步
- 将请求发送给**本地DNS服务器**，在本地域名服务器缓存中查询，如果查找到，就直接将查找结果返回，若找不到继续下一步
- 本地DNS服务器向**根域名服务器**发送请求，根域名服务器会返回一个所查询域的顶级域名服务器地址
- 本地DNS服务器向**顶级域名服务器**发送请求，接受请求的服务器查询自己的缓存，如果有记录，就返回查询结果，如果没有就返回相关的下一级的权威域名服务器的地址
- 本地DNS服务器向**权威域名服务器**发送请求，域名服务器返回对应的结果
- 本地DNS服务器将返回结果保存在缓存中，便于下次使用
- 本地DNS服务器将返回结果返回给浏览器

## 12. 简述一下TCP的三次握手

**第一次握手：** 客户端向服务端发送连接请求报文段。该报文段中包含自身的数据通讯初始序号。请求发送后，客户端便进入 SYN-SENT 状态。

**第二次握手：** 服务端收到连接请求报文段后，如果同意连接，则会发送一个应答，该应答中也会包含自身的数据通讯初始序号，发送完成后便进入 SYN-RECEIVED 状态。

**第三次握手：** 当客户端收到连接同意的应答后，还要向服务端发送一个确认报文。客户端发完这个报文段后便进入 ESTABLISHED 状态，服务端收到这个应答后也进入 ESTABLISHED 状态，此时连接建立成功。

## 13. **TCP什么要三次握手呢？两次不行吗？**

- 为了确认双方的接收能力和发送能力都正常
- 如果是用两次握手，则会出现下面这种情况：

如客户端发出连接请求，但因连接请求报文丢失而未收到确认，于是客户端再重传一次连接请求。后来收到了确认，建立了连接。数据传输完毕后，就释放了连接，客户端共发出了两个连接请求报文段，其中第一个丢失，第二个到达了服务端，但是第一个丢失的报文段只是在某些网络结点长时间滞留了，延误到连接释放以后的某个时间才到达服务端，此时服务端误认为客户端又发出一次新的连接请求，于是就向客户端发出确认报文段，同意建立连接，不采用三次握手，只要服务端发出确认，就建立新的连接了，此时客户端忽略服务端发来的确认，也不发送数据，则服务端一致等待客户端发送数据，浪费资源。

## 14. 简述一下TCP的四次挥手

**第一次挥手：** 若客户端认为数据发送完成，则它需要向服务端发送连接释放请求。

**第二次挥手**：服务端收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包，并进入 CLOSE_WAIT 状态，此时表明客户端到服务端的连接已经释放，不再接收客户端发的数据了。但是因为 TCP
连接是双向的，所以服务端仍旧可以发送数据给客户端。

**第三次挥手**：服务端如果此时还有没发完的数据会继续发送，完毕后会向客户端发送连接释放请求，然后服务端便进入 LAST-ACK 状态。

**第四次挥手：** 客户端收到释放请求后，向服务端发送确认应答，此时客户端进入 TIME-WAIT 状态。该状态会持续 2MSL（最大段生存期，指报文段在网络中生存的时间，超时会被抛弃） 时间，若该时间段内没有服务端的重发请求的话，就进入
CLOSED 状态。当服务端收到确认应答后，也便进入 CLOSED 状态。

## 15. TCP**为什么需要四次挥手呢？**

因为当服务端收到客户端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当服务端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉客户端，“你发的FIN报文我收到了”。只有等到我服务端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送，故需要四次挥手。

## 16. TCP粘包是怎么回事，如何处理?

默认情况下, TCP 连接会启⽤**延迟传送算法** (Nagle 算法), 在数据发送之前缓存他们. 如果短时间有多个数据发送, 会缓冲到⼀起作⼀次发送 (缓冲⼤⼩⻅ socket.bufferSize ), 这样可以减少 IO
消耗提⾼性能.

如果是传输⽂件的话, 那么根本不⽤处理粘包的问题, 来⼀个包拼⼀个包就好了。但是如果是多条消息, 或者是别的⽤途的数据那么就需要处理粘包.

⽽对于处理粘包的问题, 常⻅的解决⽅案有:

**多次发送之前间隔⼀个等待时间**：只需要等上⼀段时间再进⾏下⼀次 send 就好, 适⽤于交互频率特别低的场景. 缺点也很明显, 对于⽐较频繁的场景⽽⾔传输效率实在太低，不过⼏乎不⽤做什么处理.

**关闭 Nagle 算法**：关闭 Nagle 算法, 在 Node.js 中你可以通过 socket.setNoDelay() ⽅法来关闭 Nagle 算法, 让每⼀次 send
都不缓冲直接发送。该⽅法⽐较适⽤于每次发送的数据都⽐较⼤ (但不是⽂件那么⼤), 并且频率不是特别⾼的场景。如果是每次发送的数据量⽐较⼩, 并且频率特别⾼的, 关闭 Nagle 纯属⾃废武功。另外, 该⽅法不适⽤于⽹络较差的情况, 因为
Nagle 算法是在服务端进⾏的包合并情况, 但是如果短时间内客户端的⽹络情况不好, 或者应⽤层由于某些原因不能及时将 TCP 的数据 recv, 就会造成多个包在客户端缓冲从⽽粘包的情况。 (如果是在稳定的机房内部通信那么这个概率是⽐较⼩可以选择忽略的)

**进⾏封包/拆包：** 封包/拆包是⽬前业内常⻅的解决⽅案了。即给每个数据包在发送之前, 于其前/后放⼀些有特征的数据, 然后收到数据的时 候根据特征数据分割出来各个数据包。

## 17. token是什么？

1. token也可以称做**令牌**，一般由 `uid+time+sign(签名)+[固定参数]` 组成

    ```markdown
    uid: 用户唯一身份标识
    time: 当前时间的时间戳
    sign: 签名, 使用 hash/encrypt 压缩成定长的十六进制字符串，以防止第三方恶意拼接
    固定参数(可选): 将一些常用的固定参数加入到 token 中是为了避免重复查库
    ```

2. token在客户端一般存放于localStorage，cookie，或sessionStorage中。在服务器一般存于数据库中

3. token 的认证流程

    ```markdown
    用户登录，成功后服务器返回Token给客户端。
    客户端收到数据后保存在客户端
    客户端再次访问服务器，将token放入headers中 或者每次的请求 参数中
    服务器端采用filter过滤器校验。校验成功则返回请求数据，校验失败则返回错误码
    ```

4. token可以抵抗csrf，cookie+session不行

5. session时有状态的，一般存于服务器内存或硬盘中，当服务器采用分布式或集群时，session就会面对负载均衡问题。负载均衡多服务器的情况，不好确认当前用户是否登录，因为多服务器不共享session

6.

客户端登陆传递信息给服务端，服务端收到后把用户信息加密（token）传给客户端，客户端将token存放于localStorage等容器中。客户端每次访问都传递token，服务端解密token，就知道这个用户是谁了。通过cpu加解密，服务端就不需要存储session占用存储空间，就很好的解决负载均衡多服务器的问题了。这个方法叫做JWT(
Json Web Token)

## 18. token是怎么加密的

1. 需要一个secret（随机数）
2. 后端利用secret和加密算法(如：HMAC-SHA256)对payload(如账号密码)生成一个字符串(token)，返回前端
3. 前端每次request在header中带上token
4. 后端用同样的算法解密

## 19. cookie和token都放在header中，为什么会劫持cookie，不会劫持token

- **cookie**: 登陆后后端生成一个sessionId放在cookie中返回给客户端, 并且服务端一直记录着这个 sessionId, 客户端以后每次请求都会带上这个sessionId,
  服务端通过这个sessionId来验证身份之类的操作。所以别人拿到了cookie就相当于拿到了sessionId ,就可以完全替代你。同时浏览器会自动携带cookie
- token: 同样是登录后服务端返回一个token，客户端保存起来，在以后http请求里手动的加入到请求头里，服务端根据token 进行身份的校验。浏览器不会自动携带token，所以不会劫持 token。

## 20. token过期后，页面如何实现无感刷新?

**什么是无感刷新**

后台返回的token是有时效性的，时间到了，你在交互后台的时候，后台会判断你的token是否过期（安全需要），如果过期了就会逼迫你重新登陆！

**token无感刷新其本质是为了优化用户体验,当token过期时不需要用户跳回登录页重新登录,而是当token失效时,进行拦截,发送刷新token的ajax,获取最新的token进行覆盖,让用户感受不到token已经过期**

**实现无感刷新**

1、后端返回过期时间，前端判断token过期时间,去调用刷新token接口。

缺点：需要后端额外提供一个Token过期时间的字段；使用了本地时间判断，若本地时间篡改，特别是本地时间比服务器时间慢时，拦截会失败。

2、写个定时器，定时刷新Token接口。缺点：浪费资源,消耗性能,不建议采用。

3、在响应拦截器中拦截，判断Token 返回过期后，调用刷新token接口。

## 21. 介绍下304过程

- a. 浏览器请求资源时首先命中资源的Expires 和 Cache-Control，Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效，可以通过Cache-control:
  max-age指定最大生命周期，状态仍然返回200，但不会请求数据，在浏览器中能明显看到from cache字样。
- b. 强缓存失效，进入协商缓存阶段，首先验证ETagETag可以保证每一个资源是唯一的，资源变化都会导致ETag变化。服务器根据客户端上送的If-None-Match值来判断是否命中缓存。
- c.
  协商缓存Last-Modify/If-Modify-Since阶段，客户端第一次请求资源时，服务服返回的header中会加上Last-Modify，Last-modify是一个时间标识该资源的最后修改时间。再次请求该资源时，request的请求头中会包含If-Modify-Since，该值为缓存之前返回的Last-Modify。服务器收到If-Modify-Since后，根据资源的最后修改时间判断是否命中缓存

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.11.04 |