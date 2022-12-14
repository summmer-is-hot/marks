## <font color=SlateBlue>前端常见面试题(浏览器篇)二</font>

## 11. **什么是队头堵塞**

队头阻塞是由 HTTP 基本的“请求 - 应答”模型所导致的。HTTP
规定报文必须是“一发一收”，这就形成了一个先进先出的“串行”队列。队列里的请求是没有优先级的，只有入队的先后顺序，排在最前面的请求会被最优先处理。如果队首的请求因为处理的太慢耽误了时间，那么队列里后面的所有请求也不得不跟着一起等待，结果就是其他的请求承担了不应有的时间成本，造成了队头堵塞的现象。

### 11.2 **队头阻塞的解决方案：**

（1）并发连接：对于一个域名允许分配多个长连接，那么相当于增加了任务队列，不至于一个队伍的任务阻塞其它所有任务。 （2）域名分片：将域名分出很多二级域名，它们都指向同样的一台服务器，能够并发的长连接数变多，解决了队头阻塞的问题。

## 12. HTTP和HTTPS协议的区别

HTTP和HTTPS协议的主要区别如下：

- HTTPS协议需要CA证书，费用较高；而HTTP协议不需要；
- HTTP协议是超文本传输协议，信息是明文传输的，HTTPS则是具有安全性的SSL加密传输协议；
- 使用不同的连接方式，端口也不同，HTTP协议端口是80，HTTPS协议端口是443；
- HTTP协议连接很简单，是无状态的；HTTPS协议是有SSL和HTTP协议构建的可进行加密传输、身份认证的网络协议，比HTTP更加安全。

## 13. GET方法URL长度限制的原因

实际上HTTP协议规范并没有对get方法请求的url长度进行限制，这个限制是特定的**浏览器**及**服务器**对它的限制。 IE对URL长度的限制是**2083**字节(2K+35)。由于IE浏览器对URL长度的允许值是**最小的**
，所以开发过程中，只要URL不超过2083字节，那么在所有浏览器中工作都不会有问题。

```markdown
GET的长度值 = URL（2083）- （你的Domain+Path）-2（2是get请求中?=两个字符的长度）
```

下面看一下主流浏览器对get方法中url的长度限制范围：

- Microsoft Internet Explorer (Browser)：IE浏览器对URL的最大限制为**2083**个字符，如果超过这个数字，提交按钮没有任何反应。
- Firefox (Browser)：对于Firefox浏览器URL的长度限制为 **65,536** 个字符。
- Safari (Browser)：URL最大长度限制为 **80,000** 个字符。
- Opera (Browser)：URL最大长度限制为 **190,000** 个字符。
- Google (chrome)：URL最大长度限制为 **8182** 个字符。

主流的服务器对get方法中url的长度限制范围：

- Apache (Server)：能接受最大url长度为**8192**个字符。
- Microsoft Internet Information Server(IIS)：能接受最大url的长度为**16384**个字符。

根据上面的数据，可以知道，get方法中的URL长度最长不超过2083个字符，这样所有的浏览器和服务器都可能正常工作。

## 14.一个页面从输入 URL 到页面加载显示完成，这个过程中都发生了什么？

（1）**解析URL：** **首先会对 URL 进行解析，分析所需要使用的传输协议和请求的资源的路径**。如果输入的 URL 中的协议或者主机名不合法，将会把地址栏中输入的内容传递给搜索引擎。如果没有问题，浏览器会检查 URL
中是否出现了非法字符，如果存在非法字符，则对非法字符进行转义后再进行下一过程。

（2）**缓存判断：** **浏览器会判断所请求的资源是否在缓存里**，如果请求的资源在缓存里并且没有失效，那么就直接使用，否则向服务器发起新的请求。

（3）**DNS解析：** 下一步首先需要获取的是输入的 URL 中的域名的 IP 地址，首先会**判断本地是否有该域名的 IP 地址的缓存**，如果有则使用，**如果没有则向本地 DNS 服务器发起请求**。**本地 DNS
服务器也会先检查是否存在缓存**，如果**没有就会先向根域名服务器发起请求**，获得负责的顶级域名服务器的地址后，**再向顶级域名服务器请求**，然后获得负责的权威域名服务器的地址后，**再向权威域名服务器发起请求**，**最终获得域名的
IP 地址后，本地 DNS 服务器再将这个 IP 地址返回给请求的用户**。用户向本地 DNS 服务器发起请求属于递归请求，本地 DNS 服务器向各级域名服务器发起请求属于迭代请求。

（4）**获取MAC地址（选说）** 当浏览器得到 IP 地址后，**数据传输还需要知道目的主机 MAC 地址**，因为应用层下发数据给传输层，TCP 协议会指定源端口号和目的端口号，然后下发给网络层。网络层会将本机地址作为源地址，获取的
IP 地址作为目的地址。然后将下发给数据链路层，数据链路层的发送需要加入通信双方的 MAC 地址，本机的 MAC 地址作为源 MAC 地址，目的 MAC 地址需要分情况处理。通过将 IP
地址与本机的子网掩码相与，可以判断是否与请求主机在同一个子网里，如果在同一个子网里，可以使用 APR 协议获取到目的主机的 MAC 地址，如果不在一个子网里，那么请求应该转发给网关，由它代为转发，此时同样可以通过 ARP 协议来获取网关的
MAC 地址，此时目的主机的 MAC 地址应该为网关的地址。

（5）**TCP三次握手：** ，**确认客户端与服务器的接收与发送能力**，下面是 TCP 建立连接的三次握手的过程，首先客户端向服务器发送一个 SYN 连接请求报文段和一个随机序号，服务端接收到请求后向服务器端发送一个 SYN
ACK报文段，确认连接请求，并且也向客户端发送一个随机序号。客户端接收服务器的确认应答后，进入连接建立的状态，同时向服务器也发送一个ACK 确认报文段，服务器端接收到确认后，也进入连接建立状态，此时双方的连接就建立起来了。

（6）**HTTPS握手（选说）：** **如果使用的是 HTTPS 协议，在通信前还存在 TLS 的一个四次握手的过程**
。首先由客户端向服务器端发送使用的协议的版本号、一个随机数和可以使用的加密方法。服务器端收到后，确认加密的方法，也向客户端发送一个随机数和自己的数字证书。客户端收到后，首先检查数字证书是否有效，如果有效，则再生成一个随机数，并使用证书中的公钥对随机数加密，然后发送给服务器端，并且还会提供一个前面所有内容的
hash 值供服务器端检验。服务器端接收后，使用自己的私钥对数据解密，同时向客户端发送一个前面所有内容的 hash
值供客户端检验。这个时候双方都有了三个随机数，按照之前所约定的加密方法，使用这三个随机数生成一把秘钥，以后双方通信前，就使用这个秘钥对数据进行加密后再传输。

（7）**发送HTTP请求**

**服务器处理请求,返回HTTP报文**(响应)(文件)

（8）**页面渲染：** 浏览器首先会根据 html 文件(响应) **建 DOM 树**，根据解析到的 css 文件构**建 CSSOM 树**，如果遇到 script 标签，则判端是否含有 defer 或者 async 属性，要不然
script 的加载和执行会造成页面的渲染的阻塞。**当 DOM 树和 CSSOM 树建立好后，根据它们来构建渲染树**。渲染树构建好后，会根据渲染树来进行布局。布局完成后，最后使用浏览器的 UI
接口对页面进行绘制。这个时候整个页面就显示出来了。

（9）**TCP四次挥手：** **最后一步是 TCP 断开连接的四次挥手过程**。若客户端认为数据发送完成，则它需要向服务端发送连接释放请求。服务端收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包，并进入
CLOSE_WAIT 状态，此时表明客户端到服务端的连接已经释放，不再接收客户端发的数据了。但是因为 TCP
连接是双向的，所以服务端仍旧可以发送数据给客户端。服务端如果此时还有没发完的数据会继续发送，完毕后会向客户端发送连接释放请求，然后服务端便进入 LAST-ACK 状态。客户端收到释放请求后，向服务端发送确认应答，此时客户端进入
TIME-WAIT 状态。该状态会持续 2MSL（最大段生存期，指报文段在网络中生存的时间，超时会被抛弃） 时间，若该时间段内没有服务端的重发请求的话，就进入 CLOSED 状态。当服务端收到确认应答后，也便进入 CLOSED 状态。

## 15.页面有多张图片，HTTP是怎样的加载表现？

在`HTTP 1`下，浏览器对一个域名下最大TCP连接数为6，所以会请求多次。可以用**多域名部署**解决。这样可以提高同时请求的数目，加快页面图片的获取速度。

在`HTTP 2`下，可以一瞬间加载出来很多资源，因为，HTTP2支持多路复用，可以在一个TCP连接中发送多个HTTP请求。

## 16. HTTP2的头部压缩算法是怎样的？

HTTP2的头部压缩是HPACK算法。在客户端和服务器两端建立“字典”，用索引号表示重复的字符串，采用哈夫曼编码来压缩整数和字符串，可以达到50%~90%的高压缩率。

具体来说:

- 在客户端和服务器端使用“首部表”来跟踪和存储之前发送的键值对，对于相同的数据，不再通过每次请求和响应发送；
- 首部表在HTTP/2的连接存续期内始终存在，由客户端和服务器共同渐进地更新；
- 每个新的首部键值对要么被追加到当前表的末尾，要么替换表中之前的值。

## 17. HTTP请求报文的是什么样的？

请求报⽂有4部分组成:

- 请求⾏
- 请求头部
- 空⾏
- 请求体

**其中：**

（1）请求⾏包括：请求⽅法字段、URL字段、HTTP协议版本字段。它们⽤空格分隔。例如，GET /index.html HTTP/1.1。

（2）请求头部:请求头部由关键字/值对组成，每⾏⼀对，关键字和值⽤英⽂冒号“:”分隔

- User-Agent：产⽣请求的浏览器类型。
- Accept：客户端可识别的内容类型列表。
- Host：请求的主机名，允许多个域名同处⼀个IP地址，即虚拟主机。

（3）请求体: post put等请求携带的数据

## 18. HTTP响应报文的是什么样的？

请求报⽂有4部分组成:

- 响应⾏：由网络协议版本，状态码和状态码的原因短语组成，例如 HTTP/1.1 200 OK
- 响应头：响应部⾸组成
- 空⾏
- 响应体：服务器响应的数据

## 19. HTTP协议的优点和缺点

HTTP 是超文本传输协议，它定义了客户端和服务器之间交换报文的格式和方式，默认使用 80 端口。它使用 TCP 作为传输层协议，保证了数据传输的可靠性。

HTTP协议具有以下**优点**：

- 支持客户端/服务器模式
- **简单快速**：客户向服务器请求服务时，只需传送请求方法和路径。由于 HTTP 协议简单，使得 HTTP 服务器的程序规模小，因而通信速度很快。
- **无连接**：无连接就是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接，采用这种方式可以节省传输时间。
- **无状态**：HTTP 协议是无状态协议，这里的状态是指通信过程的上下文信息。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能会导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就比较快。
- **灵活**：HTTP 允许传输任意类型的数据对象。正在传输的类型由 Content-Type 加以标记。

HTTP协议具有以下**缺点**：

- **无状态：** HTTP 是一个无状态的协议，HTTP 服务器不会保存关于客户的任何信息。
- **明文传输：** 协议中的报文使用的是文本形式，这就直接暴露给外界，不安全。
- **不安全**

（1）通信使用明文（不加密），内容可能会被窃听； （2）不验证通信方的身份，因此有可能遭遇伪装； （3）无法证明报文的完整性，所以有可能已遭篡改；

## 20. 说一下HTTP 3.0

HTTP3.0，也称作HTTP over QUIC。HTTP3.0的核心是QUIC(读音quick)协议，由Google在 2015年提出的SPDY
v3演化而来的新协议，传统的HTTP协议是基于传输层TCP的协议，而QUIC是基于传输层UDP上的协议，可以定义成:HTTP3.0基于UDP的安全可靠的HTTP2.0协议。

## 21. HTTP的两种连接模式

HTTP 协议是基于 TCP/IP，并且使用了**请求-应答**的通信模式。

**HTTP协议有两种连接模式，一种是持续连接，一种非持续连接**。 （1）非持续连接指的是服务器必须为每一个请求的对象建立和维护一个全新的连接。 （2）持续连接下，TCP
连接默认不关闭，可以被多个请求复用。采用持续连接的好处是可以避免每次建立 TCP 连接三次握手时所花费的时间。

## 22. URL有哪些组成部分

以下面的URL为例www.aspxfans.com:8080/news/index?ID=246188#name

从上面的URL可以看出，一个完整的URL包括以下几部分：

- **协议部分**：该URL的协议部分为“http：”，这代表网页使用的是HTTP协议。在Internet中可以使用多种协议，如HTTP，FTP等等本例中使用的是HTTP协议。在"HTTP"后面的“//”为分隔符；
- **域名部分**：该URL的域名部分为www.aspxfans.com。一个URL中，也可以使用IP地址作为域名使用
- **端口部分**：跟在域名后面的是端口，域名和端口之间使用“:”作为分隔符。端口不是一个URL必须的部分，如果省略端口部分，将采用默认端口（HTTP协议默认端口是80，HTTPS协议默认端口是443）；
- **虚拟目录部分**：从域名后的第一个“/”开始到最后一个“/”为止，是虚拟目录部分。虚拟目录也不是一个URL必须的部分。本例中的虚拟目录是“/news/”；
- **文件名部分**
  ：从域名后的最后一个“/”开始到“？”为止，是文件名部分，如果没有“?”,则是从域名后的最后一个“/”开始到“#”为止，是文件部分，如果没有“？”和“#”，那么从域名后的最后一个“/”开始到结束，都是文件名部分。本例中的文件名是“index.asp”。文件名部分也不是一个URL必须的部分，如果省略该部分，则使用默认的文件名；
- **锚部分**：从“#”开始到最后，都是锚部分。本例中的锚部分是“name”。锚部分也不是一个URL必须的部分；
- **参数部分**：从“？”开始到“#”为止之间的部分为参数部分，又称搜索部分、查询部分。本例中的参数部分为“boardID=5&ID=24618&page=1”。参数可以允许有多个参数，参数与参数之间用“&”作为分隔符。

## 23.与缓存相关的HTTP请求头有哪些

强缓存：

- Expires
- Cache-Control

协商缓存：

- Etag、If-None-Match
- Last-Modified、If-Modified-Since

## 24. 强缓存和协商缓存

**1.强缓存：** 不会向服务器发送请求，直接从缓存中读取资源，在chrome控制台的Network选项中可以看到该请求返回200的状态码，并且size显示from disk cache或from memory
cache两种（灰色表示缓存）。

**2.协商缓存：** 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源；

> 共同点：都是从客户端缓存中读取资源； 区别是强缓存不会发请求，协商缓存会发请求。

## 25. HTTP的keep-alive有什么作用？

**http1.0默认关闭，需要手动开启。http1.1后默认开启**

**作用：** 使客户端到服务器端的链接持续有效(**长连接**)，当出现对服务器的后续请求时，keep-Alive功能避免了建立或者重新建立链接。

**使用方法：** 在请求头中加上Connection：keep-alive。

**优点：**

- 较少的CPU和内存的占用(因为要打开的连接数变少了，复用了连接)
- 减少了后续请求的延迟(无需再进行握手)

**缺点：** 本来可以释放的资源仍旧被占用。有的请求已经结束了，但是还一直连接着。

**解决方法：** 服务器设置过期时间和请求次数，超过这个时间或者次数就断掉连接。

## 26. OSI的七层模型是什么？

ISO于1978年开发的一套标准架构ISO模型，被引用来说明数据通信协议的结构和功能。

OSI在功能上可以划分为两组：

网络群组：物理层、数据链路层、网络层

使用者群组：传输层、会话层、表示层、应用层

| `OSI`七层网络模型 | `TCP/IP`四层概念模型 | 对应网络协议                                                 |
| ----------------- | -------------------- | ------------------------------------------------------------ |
| 7：应用层         | 应用层               | `HTTP`、`RTSP` `TFTP（简单文本传输协议）、`FTP`、` NFS`（数域筛法，数据加密）、`WAIS`（广域信息查询系统） |
| 6：表示层         | 应用层               | `Telnet`（internet远程登陆服务的标准协议）、`Rlogin`、`SNMP`（网络管理协议）、Gopher |
| 5：会话层         | 应用层               | `SMTP`（简单邮件传输协议）、`DNS`（域名系统）                |
| 4：传输层         | 传输层               | `TCP`（传输控制协议）、`UDP`（用户数据报协议））             |
| 3：网络层         | 网际层               | `ARP`（地域解析协议）、`RARP`、`AKP`、`UUCP`（Unix to Unix copy） |
| 2：数据链路层     | 数据链路层           | `FDDI`（光纤分布式数据接口）、`Ethernet、Arpanet、PDN`（公用数据网）、`SLIP`（串行线路网际协议）`PPP`（点对点协议，通过拨号或专线方建立点对点连接发送数据） |
| 1：物理层         | 物理层               | `SMTP`（简单邮件传输协议）、`DNS`（域名系统）                |

其中高层（7、6、5、4层）定义了应用程序的功能，下面三层（3、2、1层）主要面向通过网络的端到端的数据流

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.11.03 |
|             |           |            |
