## <font color=SlateBlue>前端常见面试题(Vue篇)（七）</font>

## Vue面试题补充

## 1.怎样理解 Vue 的单向数据流？

所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。

额外的，每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。子组件想修改时，只能通过 $emit 派发一个自定义事件，父组件接收到后，由父组件修改。

有两种常见的试图改变一个 prop 的情形 :

- **这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。** 在这种情况下，最好定义一个本地的 data 属性并将这个 prop 用作其初始值：

```vue
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```

- **这个 prop 以一种原始的值传入且需要进行转换。** 在这种情况下，最好使用这个 prop 的值来定义一个计算属性

```vue
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

## 2.在什么阶段才能访问操作DOM？

在钩子函数 mounted 被调用前，Vue 已经将编译好的模板挂载到页面上，所以在 mounted 中可以访问操作 DOM。

## 3. 父组件可以监听到子组件的生命周期吗？

比如有父组件 Parent 和子组件 Child，如果父组件监听到子组件挂载 mounted 就做一些逻辑处理，可以通过以下写法实现：

```vue
// Parent.vue
<Child @mounted="doSomething"/>
    
// Child.vue
mounted() {
  this.$emit("mounted");
}
```

以上需要手动通过 $emit 触发父组件的事件，更简单的方式可以在父组件引用子组件时通过 @hook 来监听即可，如下所示：

```vue
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
    
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},    
    
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...     
```

当然 @hook 方法不仅仅是可以监听 mounted，其它的生命周期事件，例如：created，updated 等都可以监听。

## 4.Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？

```js
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的splice变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```

我们阅读以上源码可知，vm.$set 的实现原理是：

- 如果目标是数组，直接使用数组的 splice 方法触发相应式；
- 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）

## 5. 请说下封装 vue 组件的过程

> 有复用的地方就有封装
>
> （js如此，vue也是如此）

- 1.先分析需求：确定业务需求，把页面中可以复用的结构，样式以及功能
    - 找出业务需求中存在复用的地方
- 2.具体步骤：Vue.component 或者在new Vue配置项components中, 定义组件名, 可以在props中接受给组件传的参数和值，子组件修改好数据后，想把数据传递给父组件。可以采用$emit方法

## 6. 讲一下组件的命名规范

- 给组件命名有两种方式(在Vue.Component/components时)，一种是使用链式命名"my-component"，一种是使用大驼峰命名"MyComponent"，
- 因为要遵循W3C规范中的自定义组件名 (字母全小写且必须包含一个连字符)，避免和当前以及未来的 HTML 元素相冲突

## 7.scoped作用与原理

- 作用：组件css作用域，避免`子组件`内部的css样式被`父组件`覆盖
    - 默认情况下，如果子组件和父组件css选择器权重相同，优先加载父组件css样式
- 原理：给元素添加一个自定义属性 v-data-xxx
    - `一针见血答案`： 通过属性选择题来提高css权重值

## 8. 第一次加载页面会触发哪几个钩子函数？

四个钩子

- beforeCreate,
- created,
- beforeMount,
- mounted 这几个钩子函数

## 9. Vue中如何扩展一个组件

1. 常见的组件扩展方法有：mixins，slots，extends等
2. 混入mixins是分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

```js
// 复用代码：它是一个配置对象，选项和组件里面一样
const mymixin = {
   methods: {
      dosomething(){}
   }
}
// 全局混入：将混入对象传入
Vue.mixin(mymixin)

// 局部混入：做数组项设置到mixins选项，仅作用于当前组件
const Comp = {
   mixins: [mymixin]
}
```

1. 插槽主要用于vue组件中的内容分发，也可以用于组件扩展。如果要精确分发到不同位置可以使用具名插槽，如果要使用子组件中的数据可以使用作用域插槽。
2. 组件选项中还有一个不太常用的选项extends，也可以起到扩展组件的目的
3. 混入的数据和方法**不能明确判断来源**且可能和当前组件内变量**产生命名冲突**，vue3中引入的composition api，可以很好解决这些问题，利用独立出来的响应式模块可以很方便的编写独立逻辑并提供响应式的数据，然后在setup选项中组合使用，增强代码的可读性和维护性

## 10. 如果让你从零开始写一个vue路由，说说你的思路

一个SPA应用的路由需要解决的问题是**页面跳转内容改变同时不刷新**，同时路由还需要以插件形式存在，所以：

1. 首先我会定义一个`createRouter`函数，返回路由器实例，实例内部做几件事：
    - 保存用户传入的配置项
    - 监听hash或者popstate事件
    - 回调里根据path匹配对应路由
2. 将router定义成一个Vue插件，即实现install方法，内部做两件事：
    - 实现两个全局组件：router-link和router-view，分别实现页面跳转和内容显示
    - 定义两个全局变量：route和route和route和router，组件内可以访问当前路由和路由器实例

## 11. 从0到1自己构架一个vue项目，说说有哪些步骤、哪些重要插件、目录结构你会怎么组织

1. 从0创建一个项目我大致会做以下事情：项目构建、引入必要插件、代码规范、提交规范、常用库和组件
2. 目前vue3项目我会用vite或者create-vue创建项目
3. 接下来引入必要插件：路由插件vue-router、状态管理vuex/pinia、ui库我比较喜欢element-plus和antd-vue、http工具我会选axios
4. 其他比较常用的库有vueuse，nprogress，图标可以使用vite-svg-loader
5. 下面是代码规范：结合prettier和eslint即可
6. 最后是提交规范，可以使用husky，lint-staged，commitlint

------

- 目录结构我有如下习惯： `.vscode`：用来放项目中的 vscode 配置

    `plugins`：用来放 vite 插件的 plugin 配置

    `public`：用来放一些诸如 页头icon 之类的公共文件，会被打包到dist根目录下

    `src`：用来放项目代码文件

    `api`：用来放http的一些接口配置

    `assets`：用来放一些 CSS 之类的静态资源

    `components`：用来放项目通用组件

    `layout`：用来放项目的布局

    `router`：用来放项目的路由配置

    `store`：用来放状态管理Pinia的配置

    `utils`：用来放项目中的工具方法类

    `views`：用来放项目的页面文件

## 12. 实际工作中，你总结的vue最佳实践有哪些？

### 编码风格方面：

- 命名组件时使用“多词”风格避免和HTML元素冲突
- 使用“细节化”方式定义属性而不是只有一个属性名
- 属性名声明时使用“驼峰命名”，模板或jsx中使用“肉串命名”
- 使用v-for时务必加上key，且不要跟v-if写在一起

### 性能方面：

- 路由懒加载减少应用尺寸
- 利用SSR减少首屏加载时间
- 利用v-once渲染那些不需要更新的内容
- 一些长列表可以利用虚拟滚动技术避免内存过度占用
- 对于深层嵌套对象的大数组可以使用shallowRef或shallowReactive降低开销
- 避免不必要的组件抽象

### 安全：

- 不使用不可信模板，例如使用用户输入拼接模板：`template: <div> + userProvidedString + </div>`
- 小心使用v-html，:url，:style等，避免html、url、样式等注入

## 13. 说说从 template 到 render 处理过程(compiler的工作原理)

1. Vue中有个独特的编译器模块，称为“compiler”，它的主要作用是将用户编写的template编译为js中可执行的render函数。
2. 之所以需要这个编译过程是为了便于前端程序员能高效的编写视图模板。相比而言，我们还是更愿意用HTML来编写视图，直观且高效。手写render函数不仅效率底下，而且失去了编译期的优化能力。
3. 在Vue中编译器会先对template进行解析，这一步称为parse，结束之后会得到一个JS对象，我们成为抽象语法树AST，然后是对AST进行深加工的转换过程，这一步成为transform，最后将前面得到的AST生成为JS代码，也就是render函数。

## 14. Vue实例挂载的过程中发生了什么?

1. 挂载过程指的是app.mount()过程，这个过程中整体上做了两件事：**初始化**和**建立更新机制**
2. 初始化会创建组件实例、初始化组件状态，创建各种响应式数据
3. 建立更新机制这一步会立即执行一次组件更新函数，这会首次执行组件渲染函数并执行patch将前面获得VNode转换为dom；同时首次执行渲染函数会创建它内部响应式数据之间和组件更新函数之间的依赖关系，这使得以后数据变化时会执行对应的更新函数。

## 15. 组件中的name属性有什么用？

1. 项目使用keep-alive时，可搭配组件name进行缓存过滤
2. DOM做递归组件时需要调用自身name
3. Vue-devtools调试工具里显示的组见名称是由Vue中组件name决定的
4. 动态切换组件

## 16 . 怎么在组件中监听路由参数的变化？

有两种方法可以监听路由参数的变化，但是只能用在包含的组件内。

**1. 侦听器** watch:{'this.$route'，(to,from){ //在此处监听 }, },

**2. 前置路由守卫** beforeRouteUpdate(to,from,next){ //这里监听 },

## 17. beforeDestroy钩子的作用

如果页面上有很多定时器，可以在data选项中创建一个对象timer，给每个定时器取个名字一一映射在对象timer中，在beforeDestroy构造函数中循环遍历所有定时器 ，一次性取消

```js
for(let k in this.timer){
    clearInterval(k)
    }
```

如果页面只有单个定时器，可以这么做。

```javascript
const timer= setInterval(()=>{},500);
this.$once('hook:beforeDestroy',()=>{
clearInterval(timer);
})
```

## 18. 说说在vue中踩过的坑

**1.** 给对象添加属性或者数组通过下标修改值的时候，直接通过给data里面的对象添加属性然后赋值，新添加的属性不是响应式的。

**原因：** Object.defineProperty方法拦截不到这些操作，

【解决办法】通过Vue.set(对象，属性，值)这种方式就可以达到，对象新添加的属性是响应式的。数组也可以用splice()方法修改值

**2.** 在created操作dom的时候，是报错的，获取不到dom，这个时候实例Vue实例没有挂载 【解决办法】通过：Vue.nextTick(回调函数进行获取) ， 或者在mounted钩子里获取dom

**3.** 父组件调用子组件的方法，发送请求，修改子组件数据 ，子组件的视图没有更新。

**原因**：由于Vue的DOM操作是异步的，修改数据的时候子组件的DOM还没生成，this.$refs获取不到。

【解决办法】通过：Vue.nextTick() , 在nextTick里面去发送请求修改数据。

## 19. is这个特性你用过吗？是怎么用的？

**is的作用**

**解决html模板的限制**

比如ul里面嵌套li的写法是html语法的固定写法，如果想在ul里面嵌套自己的组件，但是html在渲染dom的时候，组件对ul来说并不是有效的dom。

解决办法

```html
<ul>
  <li is='my-component'></li>
</ul>
```

**动态组件(组件切换)**

componentName可以是在本页面已经注册的局部组件名和全局组件名, 也可以是一个组件的选项对象。当控制 componentName改变时就可以动态切换选择组件。

```vue
<component :is="componentName"></component>
```

## 20. Vue 项目进行 SEO 优化

Vue SPA单页面应用对SEO不太友好，当然也有相应的解决方案，下面列出几种SEO方案

1. **SSR服务器渲染**

    服务端渲染, 在服务端html页面节点, 已经解析创建完了, 浏览器直接拿到的是解析完成的页面解构

    关于服务器渲染：[Vue官网介绍](https://link.juejin.cn?target=https%3A%2F%2Fssr.vuejs.org%2Fzh%2F%23%E4%BB%80%E4%B9%88%E6%98%AF%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%B8%B2%E6%9F%93-ssr-%EF%BC%9F) ，对Vue版本有要求，对服务器也有一定要求，需要支持nodejs环境。

    优势: 更好的 SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面

    缺点: 服务器nodejs环境的要求, 且对原代码的改造成本高! `nuxt.js` (坑比较多, 做好踩坑的准备)

2. **静态化 (博客, 介绍性官网)**

    Nuxt.js 可以进行 generate 静态化打包, 缺点: 动态路由会被忽略。 `/users/:id`

    优势：

    - 编译打包时, 就会帮你处理, 纯静态文件，访问速度超快；
    - 对比SSR，不涉及到服务器负载方面问题；
    - 静态网页不宜遭到黑客攻击，安全性更高。

    不足：

    - 如果动态路由参数多的话不适用。

3. **预渲染 prerender-spa-plugin (插件)**

    如果你只是对少数页面需要做SEO处理（例如 / 首页, /about 关于等页面）

    预渲染是一个非常好的方式, 预渲染会在构建时, 简单的针对特定路由, 生成静态 HTML 文件 (打包时可以帮你解析静态化)

    优势: 设置预渲染简单, 对代码的改动小

    缺点: 只适合于做少数页面进行SEO的情况, 如果页面几百上千, 就不推荐了 (会打包很慢)

4. **使用Phantomjs `针对爬虫` 做处理**

    Phantomjs是一个基于webkit内核的无头浏览器，没有UI界面，就是一个浏览器，

    其内的点击、翻页等人为相关操作需要程序设计实现。

    这种解决方案其实是一种旁路机制，原理就是通过Nginx配置， 判断访问的来源UA是否是爬虫访问，

    如果是则将搜索引擎的爬虫请求转发到一个node server，再通过PhantomJS来解析完整的HTML，返回给爬虫

    优势：

    - 完全不用改动项目代码，按原本的SPA开发即可，对比开发SSR成本小太多了；
    - 对已用SPA开发完成的项目，这是不二之选。

    不足：

    - 部署需要node服务器支持；

    - 爬虫访问比网页访问要慢一些，因为定时要定时资源加载完成才返回给爬虫；(不影响用户的访问)

    - 如果被恶意模拟百度爬虫大量循环爬取，会造成服务器负载方面问题，

        解决方法是判断访问的IP，是否是百度官方爬虫的IP。

**小结:**

- 如果构建大型网站，如商城类 => SSR服务器渲染
- 如果只是正常公司官网, 博客网站等 => 预渲染/静态化/Phantomjs 都比较方便
- 如果是已用SPA开发完成的项目进行SEO优化，而且部署环境支持node服务器，使用 Phantomjs

## 21. 后端接口还没有开发好，怎么使用mock数据

Mock: 模拟数据；拦截请求；

```javascript
// mock/index.js
Mock.mock('/api/users', 'get', (req, res) => {
  // 通过 req 拿到前端的信息
  // 根据此信息返回对应的数据（Mock 的数据）
  res.send({ mock的数据 })
})
// main.js
import './mock'
```

## 22. vue动画怎么实现

当vue中，显示隐藏，创建移除，一个元素或者一个组件的时候，可以通过transition实现动画。

- 进入（显示，创建）
- - v-enter-from 进入前
    - v-enter-active 进入中
    - v-enter-to 进入后
- 离开（隐藏，移除）
- - v-leave-from 进入前
    - v-leave-active 进入中
    - v-leave-to 进入后

两个步骤

1. 给要加动画的盒子,包裹一个transition标签
2. 在动画类名中写样式

多个transition使用不同动画，可以添加nam属性，name属性的值替换v即可。

## 23. Vue初始化过程中（new Vue(options)）都做了什么？

- 处理组件配置项；初始化根组件时进行了选项合并操作，将全局配置合并到根组件的局部配置上；初始化每个子组件时做了一些性能优化，将组件配置对象上的一些深层次属性放到 vm.$options 选项中，以提高代码的执行效率；
- 初始化组件实例的关系属性，比如 p a r e n t 、 parent、parent、children、r o o t 、 root、root、refs 等 处理自定义事件
- 调用 beforeCreate 钩子函数
- 初始化组件的 inject 配置项，得到 ret[key] = val 形式的配置对象，然后对该配置对象进行响应式处理，并代理每个 key 到 vm 实例上
- 数据响应式，处理 props、methods、data、computed、watch 等选项
- 解析组件配置项上的 provide 对象，将其挂载到 vm._provided 属性上 调用 created 钩子函数
- 如果发现配置项上有 el 选项，则自动调用 mount方法，也就是说有了el选项，就不需要再手动调用mount 方法，也就是说有了 el 选项，就不需要再手动调用 mount方法，也就是说有了el选项，就不需要再手动调用mount 方法，反之，没提供 el 选项则必须调用 $mount

## 24. vue中data的属性可以和methods中方法同名吗，为什么？

可以同名，methods的方法名会被data的属性覆盖；调试台也会出现报错信息，但是不影响执行；

## 25. 什么是函数式组件？

函数式组件，我们可以理解为没有内部状态，没有生命周期钩子函数，没有`this`(不需要实例化的组件)。

**为什么使用函数式组件**

1. 最主要最关键的原因是函数式组件不需要实例化，无状态，没有生命周期，所以渲染性能要好于普通组件
2. 函数式组件结构比较简单，代码结构更清晰

**函数式组件与普通组件的区别**

函数式组件需要在声明组件是指定functional

函数式组件不需要实例化，所以没有`this`,`this`通过`render`函数的第二个参数来代替

函数式组件没有生命周期钩子函数，不能使用计算属性，watch等等

函数式组件不能通过$emit对外暴露事件，调用事件只能通过`context.listeners.click`的方式调用外部传入的事件

因为函数式组件是没有实例化的，所以在外部通过`ref`去引用组件时，实际引用的是`HTMLElement`

函数式组件的`props`可以不用显示声明，所以没有在`props`里面声明的属性都会被自动隐式解析为`prop`,而普通组件所有未声明的属性都被解析到`$attrs`里面，并自动挂载到组件根元素上面(可以通过`inheritAttrs`属性禁止)

## 26. Vue2怎么内部监听生命周期钩子(hook)

在`Vue`组件中，可以用过`$on`,`$once`去监听所有的生命周期钩子函数，如监听组件的`updated`钩子函数可以写成 `this.$on('hook:updated', () => {})`

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.25 |
