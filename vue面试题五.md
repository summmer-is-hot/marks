## <font color=SlateBlue>前端常见面试题(Vue篇)（五）</font>

## Vue-Router 路由

## 1. 对前端路由的理解

在前端技术早期，一个 url 对应一个页面，如果要从 A 页面切换到 B 页面，那么必然伴随着页面的刷新。这个体验并不好，不过在最初也是无奈之举——用户只有在刷新页面的情况下，才可以重新去请求数据。

后来，改变发生了——Ajax 出现了，它允许人们在不刷新页面的情况下发起请求；与之共生的，还有“不刷新页面即可更新页面内容”这种需求。在这样的背景下，出现了 **SPA（单页面应用**）。

SPA极大地提升了用户体验，它允许页面在不刷新的情况下更新页面内容，使内容的切换更加流畅。但是在 SPA 诞生之初，人们并没有考虑到“定位”这个问题——在内容切换前后，页面的 URL 都是一样的，这就带来了两个问题：

- SPA 其实并不知道当前的页面“进展到了哪一步”。可能在一个站点下经过了反复的“前进”才终于唤出了某一块内容，但是此时只要刷新一下页面，一切就会被清零，必须重复之前的操作、才可以重新对内容进行定位——SPA 并不会“记住”你的操作。
- 由于有且仅有一个 URL 给页面做映射，这对 SEO 也不够友好，搜索引擎无法收集全面的信息

**为了解决这个问题，前端路由出现了**。

前端路由可以帮助我们在仅有一个页面的情况下，“记住”用户当前走到了哪一步——为 SPA 中的各个视图匹配一个唯一标识。这意味着用户前进、后退触发的新内容，都会映射到不同的 URL 上去。此时即便他刷新页面，因为当前的 URL 可以标识出他所处的位置，因此内容也不会丢失。

那么如何实现这个目的呢？首先要解决两个问题：

- 当用户刷新页面时，浏览器会默认根据当前 URL 对资源进行重新定位（发送请求）。这个动作对 SPA 是不必要的，因为我们的 SPA 作为单页面，无论如何也只会有一个资源与之对应。此时若走正常的请求-刷新流程，反而会使用户的前进后退操作无法被记录。
- 单页面应用对服务端来说，就是一个URL、一套资源，那么如何做到用“不同的URL”来映射不同的视图内容呢？

从这两个问题来看，服务端已经完全救不了这个场景了。所以要靠咱们前端自力更生，不然怎么叫“前端路由”呢？作为前端，可以提供这样的解决思路：

- 拦截用户的刷新操作，避免服务端盲目响应、返回不符合预期的资源内容。把刷新这个动作完全放到前端逻辑里消化掉。
- 感知 URL 的变化。这里不是说要改造 URL、凭空制造出 N 个 URL 来。而是说 URL 还是那个 URL，只不过我们可以给它做一些微小的处理——这些处理并不会影响 URL 本身的性质，不会影响服务器对它的识别，只有我们前端感知的到。一旦我们感知到了，我们就根据这些变化、用 JS 去给它生成不同的内容。

## 2. VueRouter是什么, 有那些组件

- Vue Router 是官方的路由管理器。它和 Vue.js 的核心深度集成，路径和组件的映射关系, 让构建单页面应用变得易如反掌。
- router-link 实质上最终会渲染成a链接
- router-view 子级路由显示
- keep-alive 包裹组件缓存

## 3. route和route 和route和router 的区别

- $route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- $router 是“路由实例”对象包括了路由的跳转方法，钩子函数等。

## 4. 路由开发的优缺点

优点：

- 整体不刷新页面，用户体验更好
- 数据传递容易, 开发效率高

缺点：

- 开发成本高(需要学习专门知识)
- 首次加载会比较慢一点。不利于seo

## 5. VueRouter的使用方式

1. 使用Vue.use( )将VueRouter插入
2. 创建路由规则
3. 创建路由对象
4. 将路由对象挂到 Vue 实例上
5. 设置路由挂载点

## 6. vue-router 路由模式有几种？

**hash模式、history模式、Abstract模式**

### 前端路由原理

前端路由的核心，就在于改变视图的同时不会向后端发出请求；而是加载路由对应的组件

vue-router就是将组件映射到路由, 然后渲染出来的。并实现了三种模式

Hash模式、History模式以及Abstract模式。默认Hash模式

**hash模式**

是指 url 尾巴后的 # 号以及后面的字符。hash 虽然出现在url中，但不会被包括在http请求中，对后端完全没有影响，因此改变hash不会被重新加载页面。

**history 模式**

URL 就像正常的 url, 不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了

**Abstract模式**

支持所有javascript运行模式。vue-router 自身会对环境做校验，如果发现没有浏览器的 API，路由会自动强制进入 abstract 模式。在移动端原生环境中也是使用 abstract 模式。

**修改路由模式:** 在实例化路由对象时, 传入mode选项和值修改

### Hash模式

**原理** **基于浏览器的hashchange事件**，地址变化时，通过window.location.hash 获取地址上的hash值；并通过构造Router类，配置routes对象设置hash值与对应的组件内容。

**优点**

1. **hash值会出现在URL中, 但是不会被包含在Http请求中, 因此hash值改变不会重新加载页面**
2. **hash改变会触发hashchange事件, 能控制浏览器的前进后退**
3. **兼容性好**

**缺点**

1. 地址栏中携带#，不美观
2. 只可修改 # 后面的部分，因此只能设置与当前 URL 同文档的 URL
3. hash有体积限制，故只可添加短字符串
4. **设置的新值必须与原来不一样才会触发hashchange事件**，并将记录添加到栈中
5. **每次URL的改变不属于一次http请求，所以不利于SEO优化**

### History模式

**原理**

**基于HTML5新增的pushState()和replaceState()两个api，以及浏览器的popstate事件**，地址变化时，通过window.location.pathname找到对应的组件。并通过构造Router类，配置routes对象设置pathname值与对应的组件内容。

**优点**

1. 没有#，更加美观
2. pushState() 设置的新 URL 可以是与当前 URL 同源的任意 URL
3. pushState() 设置的新 URL 可以与当前 URL 一模一样，这样也会把记录添加到栈中
4. pushState() 通过 stateObject 参数可以添加任意类型的数据到记录中
5. pushState() 可额外设置 title 属性供后续使用
6. 浏览器的进后退能触发浏览器的popstate事件，获取window.location.pathname来控制页面的变化

**缺点**

1. URL的改变属于http请求，借助history.pushState实现页面的无刷新跳转，因此会重新请求服务器。所以**前端的 URL 必须和实际向后端发起请求的 URL 一致**。如果用户输入的URL回车或者浏览器刷新或者分享出去某个页面路径，用户点击后，URL与后端配置的页面请求URL不一致，则匹配不到任何静态资源，就会返回404页面。所以需要后台配置支持，覆盖所有情况的候选资源，如果 URL 匹配不到任何静态资源，则应该返回app 依赖的页面或者应用首页。
2. **兼容性差**，特定浏览器支持

### 为什么history模式下路由跳转会报404

1. URL的改变属于http请求，借助history.pushState实现页面的无刷新跳转，因此会重新请求服务器
2. 所以前端的 URL 必须和实际向后端发起请求的 URL 一致

## 7. 路由跳转有那些方式

1、this.$router.push()跳转到指定的url，并在history中添加记录，点击回退返回到上一个页面

2、this.$router.replace()跳转到指定的url，但是history中不会添加记录，点击回退到上上个页面

3、this.$router.go(n)向前或者后跳转n个页面，n可以是正数也可以是负数

### 编程式导航使用的方法以及常用的方法

- 路由跳转 ： this.$router.push()
- 路由替换 : this.$router.replace()
- 后退： this.$router.back()
- 前进 ：this.$router.forward()

## 8. Vue-router跳转和location.href有什么区别

- 使用 `location.href= /url`来跳转，简单方便，但是刷新了页面；
- 使用 `history.pushState( /url )` ，无刷新页面，静态跳转；
- 引进 router ，然后使用 `router.push( /url )` 来跳转，使用了 `diff` 算法，实现了按需加载，减少了 dom 的消耗。其实使用 router 跳转和使用 `history.pushState()` 没什么差别的，因为vue-router就是用了 `history.pushState()` ，尤其是在history模式下。

## 9. 如何获取页面的hash变化

**（1）监听$route的变化**

![1.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5_1.webp?)

**（2）window.location.hash读取#值**

window.location.hash 的值可读可写，读取来判断状态是否改变，写入时可以在不重载网页的前提下，添加一条历史访问记录。

## 10. 路由的传参方式

### 声明式导航传参

在 router-link 上的 to 属性传值,

1. /path?参数名=值
    - 接收传递过来的值: $route.query.参数名
2. /path/值/值 –> 需要路由对象提前配置 path: “/path/参数名”
    - 接收传递过来的值: $route.params.参数名

### 编程式导航传参

this.$router.push( ) 可以不参数,根据传的值自动匹配是path还是name

因为使用path会自动忽略params ,所以会出现两种组合

**(1) name+params 方式传参**

A页面传参

```vue
this.$router.push({
    name: 'xxx', // 跳转的路由
    params: {
      id: id   // 发送的参数
    }
})
```

B页面接收传参：

this.$route.params.id

**(2) path+query 方式传参**

A页面传参

```vue
this.$router.push({
    path: '/xxx', // 跳转的路由
    query: {
      id: id    // 发送的参数
    }
})
```

B页面接参：

this.$route.query.id

**params 和query 方式传参的区别**

1. 写法上不同
2. 地址栏不同
3. 刷新方式不同

## 11. params和query的区别

**用法**：query要用path来引入，params要用name来引入，接收参数都是类似的，分别是 `this.$route.query.name` 和 `this.$route.params.name` 。

**url地址显示**：query更加类似于ajax中get传参，params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示

**注意**：query刷新不会丢失query里面的数据 params刷新会丢失 params里面的数据。

## 12. 路由配置项常用的属性及作用

路由配置参数：

path : 跳转路径  component : 路径相对于的组件  name:命名路由  children:子路由的配置参数(路由嵌套)  props:路由解耦  redirect : 重定向路由

## 13. 路由重定向和404

**路由重定向**

1. 匹配path后, 强制切换到另一个目标path上
2. redirect 是设置要重定向到哪个路由路径
3. 网页默认打开, 匹配路由"/", 强制切换到"/find"上
4. redirect配置项, 值为要强制切换的路由路径
5. 强制重定向后, 还会重新来数组里匹配一次规则

**404页面**

1. 如果路由hash值, 没有和数组里规则匹配
2. path: ' * ' (任意路径)
3. 默认给一个404页面
4. 如果路由未命中任何规则, 给出一个兜底的404页面

## 14. Vue-router 导航守卫有哪些

- 全局守卫：**beforeEach**、beforeResolve、**afterEach**
- 路由独享的守卫：beforeEnter
- 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

## 15. Vue-router 路由钩子在生命周期的体现

- ### Vue-Router导航守卫

有的时候，需要通过路由来进行一些操作，比如最常见的登录权限验证，当用户满足条件时，才让其进入导航，否则就取消跳转，并跳到登录页面让其登录。 为此有很多种方法可以植入路由的导航过程：全局的，单个路由独享的，或者组件级的

**1. 全局路由钩子**

vue-router全局有三个路由钩子;

- router.beforeEach 全局前置守卫 进入路由之前
- router.beforeResolve 全局解析守卫（2.5.0+）在 beforeRouteEnter 调用之后调用
- router.afterEach 全局后置钩子 进入路由之后

具体使用∶

- beforeEach（判断是否登录了，没登录就跳转到登录页）

![2.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5_2.webp?)

- afterEach （跳转之后滚动条回到顶部）

![3.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5_3.webp?)

**2. 单个路由独享钩子**

**beforeEnter** 如果不想全局配置守卫的话，可以为某些路由单独配置守卫，有三个参数∶ to、from、next

![4.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5_4.webp?)

**3. 组件内钩子**

beforeRouteUpdate、beforeRouteEnter、beforeRouteLeave

这三个钩子都有三个参数∶to、from、next

- beforeRouteEnter∶ 进入组件前触发
- beforeRouteUpdate∶ 当前地址改变并且改组件被复用时触发，举例来说，带有动态参数的路径foo/∶id，在 /foo/1 和 /foo/2 之间跳转的时候，由于会渲染同样的foa组件，这个钩子在这种情况下就会被调用
- beforeRouteLeave∶ 离开组件被调用

注意点，beforeRouteEnter组件内还访问不到this，因为该守卫执行前组件实例还没有被创建，需要传一个回调给 next来访问，例如：

![5.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5_5.webp?)

- ### Vue路由钩子在生命周期函数的体现

**1. 完整的路由导航解析流程（不包括其他生命周期）**

- 触发进入其他路由。
- 调用要离开路由的组件守卫beforeRouteLeave
- 调用局前置守卫∶ beforeEach
- 在重用的组件里调用 beforeRouteUpdate
- 调用路由独享守卫 beforeEnter。
- 解析异步路由组件。
- 在将要进入的路由组件中调用 beforeRouteEnter
- 调用全局解析守卫 beforeResolve
- 导航被确认。
- 调用全局后置钩子的 afterEach 钩子。
- 触发DOM更新（mounted）。
- 执行beforeRouteEnter 守卫中传给 next 的回调函数

**2. 触发钩子的完整顺序**

路由导航、keep-alive、和组件生命周期钩子结合起来的，触发顺序，假设是从a组件离开，第一次进入b组件∶

- beforeRouteLeave：路由组件的组件离开路由前钩子，可取消路由离开。
- beforeEach：路由全局前置守卫，可用于登录验证、全局路由loading等。
- beforeEnter：路由独享守卫
- beforeRouteEnter：路由组件的组件进入路由前钩子。
- beforeResolve：路由全局解析守卫
- afterEach：路由全局后置钩子
- beforeCreate：组件生命周期，不能访问tAis。
- created;组件生命周期，可以访问tAis，不能访问dom。
- beforeMount：组件生命周期
- deactivated：离开缓存组件a，或者触发a的beforeDestroy和destroyed组件销毁钩子。
- mounted：访问/操作dom。
- activated：进入缓存组件，进入a的嵌套子组件（如果有的话）。
- 执行beforeRouteEnter回调函数next。

**3. 导航行为被触发到导航完成的整个过程**

- 导航行为被触发，此时导航未被确认。
- 在失活的组件里调用离开守卫 beforeRouteLeave。
- 调用全局的 beforeEach守卫。
- 在重用的组件里调用 beforeRouteUpdate 守卫(2.2+)。
- 在路由配置里调用 beforeEnter。
- 解析异步路由组件（如果有）。
- 在被激活的组件里调用 beforeRouteEnter。
- 调用全局的 beforeResolve 守卫（2.5+），标示解析阶段完成。
- 导航被确认。
- 调用全局的 afterEach 钩子。
- 非重用组件，开始组件实例的生命周期：beforeCreate&created、beforeMount&mounted
- 触发 DOM 更新。
- 用创建好的实例调用 beforeRouteEnter守卫中传给 next 的回调函数。
- 导航完成

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.23 |
