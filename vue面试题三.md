## <font color=SlateBlue>前端常见面试题(Vue篇)（三）</font>

## 20. 子组件可以直接改变父组件的数据吗？

子组件不可以直接改变父组件的数据。这样做主要是为了维护父子组件的单向数据流。每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。如果这样做了，Vue 会在浏览器的控制台中发出警告。

Vue提倡单向数据流，即父级 props 的更新会流向子组件，但是反过来则不行。这是为了防止意外的改变父组件状态，使得应用的数据流变得难以理解，导致数据流混乱。如果破坏了单向数据流，当应用复杂时，debug 的成本会非常高。

**只能通过 `$emit` 派发一个自定义事件，父组件接收到后，由父组件修改。**

## 21. 生命周期

### (1). 说一下Vue的生命周期

vue 实例从创建到销毁的过程就是生命周期。

也就是从开始创建、初始化数据、编译模板、挂在 dom -> 渲染、更新 -> 渲染、准备销毁、销毁在等一系列过程

vue的声明周期常见的主要分为4大阶段8大钩子函数

**第一阶段：创建前 / 后**

**beforeCreate（创建前）** ：数据观测和初始化事件还未开始，此时 data 的响应式追踪、event/watcher 都还没有被设置，也就是说不能访问到data、computed、watch、methods上的方法和数据。

**created（创建后）** ：实例创建完成，实例上配置的 options 包括 data、computed、watch、methods 等都配置完成，但是此时渲染得节点还未挂载到 DOM，所以不能访问到 `$el` 属性。

**第二阶段: 渲染前 / 后**

**beforeMount（挂载前）** ：在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。此时还没有挂载html到页面上。

**mounted（挂载后）** ：在el被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html 页面中。此过程中进行ajax交互。

**第三阶段: 更新前 / 后**

**beforeUpdate（更新前）** ：响应式数据更新时调用，此时虽然响应式数据更新了，但是对应的真实 DOM 还没有被渲染。

**updated（更新后）** ：在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。此时 DOM 已经根据响应式数据的变化更新了。调用时，组件 DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。

**第四阶段: 销毁前 / 后**

**beforeDestroy（销毁前）** ：实例销毁之前调用。这一步，实例仍然完全可用，`this` 仍能获取到实例。

**destroyed（销毁后）** ：实例销毁后调用，调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务端渲染期间不被调用。

**另外三个生命周期函数不常用**

另外还有 `keep-alive` 独有的生命周期，分别为 `activated` 和 `deactivated` 。用 `keep-alive` 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 `deactivated` 钩子函数，命中缓存渲染后会执行 `activated` 钩子函数。

`errorCapured`钩子，当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 `false` 以阻止该错误继续向上传播。

![1.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3_1.webp?)

![2.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3_2.webp?)

### (2). Vue 子组件和父组件执行顺序

**加载渲染过程：**

1. 父组件 beforeCreate
2. 父组件 created
3. 父组件 beforeMount
4. 子组件 beforeCreate
5. 子组件 created
6. 子组件 beforeMount
7. 子组件 mounted
8. 父组件 mounted

**更新过程：**

1. 父组件 beforeUpdate
2. 子组件 beforeUpdate
3. 子组件 updated
4. 父组件 updated

**销毁过程：**

1. 父组件 beforeDestroy
2. 子组件 beforeDestroy
3. 子组件 destroyed
4. 父组件 destroyed

### (3). created和mounted的区别

- `created`:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
- `mounted`:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。

### (4). 一般在哪个生命周期请求异步数据

我们可以在钩子函数`created`、`beforeMount`、`mounted` 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。

推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面加载时间，用户体验更好；
- SSR不支持 beforeMount 、mounted 钩子函数，放在 created 中有助于一致性。

## 22. 组件缓存 keep-alive

**组件缓存**

组件的缓存可以在进行动态组件切换的时候对组件内部数据进行缓存,而不是走销毁流程

使用场景: 多表单切换,对表单内数据进行保存

**keep-alive**

包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。

是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。 当组件在`<keep-alive>`内被切换，它的`activated`和 `deactivated`这两个生命周期钩子函数将会被对应执行 。

### keep-alive的参数(include,exclude)

- include(包含): 名称匹配的组件会被缓存-->include的值为组件的name。
- exclude(排除): 任何名称匹配的组件都不会被缓存。
- max - 数量 决定最多可以缓存多少组件。

### **keep-alive的使用**

1. 搭配`<component></component>`使用
2. 搭配路由使用 ( 需配置路由meta信息的`keepAlive`属性 )
3. 清除缓存组件
    - 在组件跳转之前使用后置路由守卫判断组件是否缓存
    - ( beforeRouteLeave( to, from, next ){ from.meta.keepAlive = false }

### **keep-alive的两个钩子函数**

| activated                            | deactivated                      |
| ------------------------------------ | -------------------------------- |
| 在 `keep-alive` 组件激活时调用       | 在`keep-alive` 组件停用时调用    |
| 该钩子函数在服务器端渲染期间不被调用 | 该钩子在服务器端渲染期间不被调用 |

使用`keep-alive`会将数据保留在内存中，如果要在每次进入页面的时候获取最新的数据，需要在 `activated`阶段获取数据，承担原来`created`钩子函数中获取数据的任务。

**注意：** 只有组件被`keep-alive`包裹时，这两个生命周期函数才会被调用，如果作为正常组件使用，是不会被调用的

使用 exclude 排除之后，就算被包裹在 keep-alive 中，这两个钩子函数依然不会被调用！在服务端渲染时，此钩子函数也不会被调用。

设置了缓存的组件钩子调用情况：

第一次进入：beforeRouterEnter ->created->…->activated->…->deactivated> beforeRouteLeave

后续进入时：beforeRouterEnter ->activated->deactivated> beforeRouteLeave

### **keep-alive主要流程**

1. 判断组件 name ，不在 include 或者在 exclude 中，直接返回 VNode，说明该组件不被缓存。
2. 获取组件实例 key ，如果有获取实例的 key，否则重新生成。
3. key生成规则，cid +"∶∶"+ tag ，仅靠cid是不够的，因为相同的构造函数可以注册为不同的本地组件。
4. 如果缓存对象内存在，则直接从缓存对象中获取组件实例给 VNode ，不存在则添加到缓存对象中。 5.最大缓存数量，当缓存组件数量超过 max 值时，清除 keys 数组内第一个组件。

### **keep-alive 的实现**

![3.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3_3.webp?)

## 23. 过滤器的作用，如何实现一个过滤器

根据过滤器的名称，过滤器是用来过滤数据的，在Vue中使用`filters`来过滤数据，`filters`不会修改数据，而是过滤数据，改变用户看到的输出（计算属性 `computed` ，方法 `methods` 都是通过修改数据来处理数据格式的输出显示）。

**使用场景：**

- 需要格式化数据的情况，比如需要处理时间、价格等数据格式的输出 / 显示。
- 比如后端返回一个 **年月日的日期字符串**，前端需要展示为 **多少天前** 的数据格式，此时就可以用`fliters`过滤器来处理数据。

过滤器是一个函数，它会把表达式中的值始终当作函数的第一个参数。过滤器用在**插值表达式 {{ }} 和 v-bind 表达式** 中，然后放在操作符“ `|` ”后面进行指示。

例如，在显示金额，给商品价格添加单位：

![4.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3_4.webp?)

## 24. 说说你对slot插槽的理解？slot使用场景有哪些？

### **slot是什么**

`slot`又名插槽，**是Vue的内容分发机制**，组件内部的模板引擎使用slot元素作为承载分发内容的出口。插槽slot是子组件的一个模板标签元素，而这一个标签元素是否显示，以及怎么显示是由父组件决定的。

通过插槽可以让用户可以拓展组件，去更好地复用组件和对其做定制化处理

通过`slot`插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用

比如布局组件、表格列、下拉选、弹框显示内容等

**分类**

### **默认插槽**

子组件用`<slot>`标签来确定渲染的位置，标签里面可以放`DOM`结构，当父组件使用的时候没有往插槽传入内容，标签内`DOM`结构就会显示在页面

父组件在使用的时候，直接在子组件的标签内写入内容即可

### **具名插槽**

子组件用`name`属性来表示插槽的名字，不传为默认插槽

父组件中在使用时在默认插槽的基础上加上`slot`属性，值为子组件插槽`name`属性值

### **作用域插槽**

子组件在作用域上绑定属性来将子组件的信息传给父组件使用，这些属性会被挂在父组件`v-slot`接受的对象上

父组件中在使用时通过`v-slot:`（简写：#）获取子组件的信息，在内容中使用

### **小结**

- `v-slot`属性只能在`<template>`上使用，但在只有默认插槽时可以在组件标签上使用
- 默认插槽名为`default`，可以省略default直接写`v-slot`
- 缩写为`#`时不能不写参数，写成`#default`
- 可以通过解构获取`v-slot={user}`，还可以重命名`v-slot="{user: newName}"`和定义默认值`v-slot="{user = '默认值'}"`

## 25. Vue为什么采用异步渲染呢？

`Vue` 是组件级更新，如果不采用异步更新，那么每次更新数据都会对当前组件进行重新渲染，所以为了性能，`Vue` 会在本轮数据更新后，在异步更新视图。核心思想`nextTick` 。

`dep.notify（）` 通知 watcher进行更新，`subs[i].update` 依次调用 watcher 的`update` ，`queueWatcher` 将watcher 去重放入队列， nextTick（`flushSchedulerQueue` ）在下一tick中刷新watcher队列（异步）。

## 25.2 $nextTick 原理及作用

其实一句话就可以把`$nextTick`这个东西讲明白：就是你放在`$nextTick`当中的操作不会立即执行，而是等数据更新、DOM更新完成之后再执行，这样我们拿到的肯定就是最新的了。

Vue的响应式并不是只数据发生变化之后，DOM就立刻发生变化，而是按照一定的策略进行DOM的更新。

DOM更新有两种选择，一个是在本次事件循环的最后进行一次DOM更新，另一种是把DOM更新放在下一轮的事件循环当中。Vue优先选择第一种，只有当环境不支持的时候才触发第二种机制。

虽然性能上提高了很多，但这个时候问题就出现了。我已经把数据改掉了，但是它的更新异步的，而我在获取的时候，它还没有来得及改，这个时候就需要用到nextTick

**原理：**

**Vue 的 nextTick 其本质是对 JavaScript 执行原理 `EventLoop` 的一种应用。**

- Vue2刚开始的时候, $nextTick是宏任务(setTimeout)，但是宏任务的性能太差。
- 后来改成了微任务Mutation Observer，但是还是有一些问题：
    - 速度太快了，在一些特殊场景下，DOM还没更新就去获取了
    - 兼容性不好，很多浏览器不支持
- 后来又更新成了微宏并行阶段：先判断是否支持Mutation Observer，如果支持就使用，否则使用宏任务
- Vue2.5版本之后，修复了微任务的那些问题，目前最新的$nextTick采用的是纯微任务。

由于Vue的DOM操作是异步的，所以，在上面的情况中，就要将DOM2获取数据的操作写在`$nextTick`中。

![5.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3_5.webp?)

所以，在以下情况下，会用到nextTick：

- **在数据变化后执行的某个操作，而这个操作需要使用随数据变化而变化的DOM结构的时候，这个操作就需要方法在`nextTick()`的回调函数中。**
- **在vue生命周期中，如果在created()钩子进行DOM操作，也一定要放在`nextTick()`的回调函数中。**

因为在created()钩子函数中，页面的DOM还未渲染，这时候也没办法操作DOM，所以，此时如果想要操作DOM，必须将操作的代码放在`nextTick()`的回调函数中。

## 26. 描述下Vue2的自定义指令

在 Vue2.0 中，代码复用和抽象的主要形式是组件。然而，有的情况下，你仍然需要对普通 DOM 元素进行底层操作，这时候就会用到自定义指令。 一般需要对DOM元素进行底层操作时使用，尽量只用来操作 DOM展示，不修改内部的值。当使用自定义指令直接修改 value 值时绑定v-model的值也不会同步更新；如必须修改可以在自定义指令中使用keydown事件，在vue组件中使用 change事件，回调中修改vue数据;

**（1）自定义指令基本内容**

- 全局定义：`Vue.directive("focus",{})`

- 局部定义：`directives:{focus:{}}`

- 钩子函数：指令定义对象提供钩子函数

    1. ```markdown
         bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
        ```

    2. ```markdown
         inserted：被绑定元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中）。
        ```

    3. ```markdown
         update：所在组件的VNode更新时调用，但是可能发生在其子VNode更新之前调用。指令的值可能发生了改变，
         也可能没有。但是可以通过比较更新前后的值来忽略不必要的模板更新。
        ```

    4. ```markdown
         ComponentUpdate：指令所在组件的 VNode及其子VNode全部更新后调用。
        ```

    5. ```markdown
         unbind：只调用一次，指令与元素解绑时调用。
        ```

- 钩子函数的参数 ：

    1. el：指令所绑定的元素，可以用来直接操作 DOM
    2. bing： 一个对象，包含以下属性：
        - name: 指令名，不包括 v- 前缀。
        - value: 指令的绑定值， 例如： v-my-directive="1 + 1", value 的值是2。
        - oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated钩子中可用。无论值是否改变都可用。
        - expression: 绑定值的表达式或变量名。 例如 v-my-directive="1 + 1",    expression 的值是 "1 + 1"。
        - arg: 传给指令的参数。例如 v-my-directive:foo， arg 的值是 "foo"。
        - modifiers: 一个包含修饰符的对象。 例如： v-my-directive.foo.bar, 修饰符对象 modifiers 的值是 { foo: true, bar: true }。
    3. VNode： 编译生成的虚拟节点
    4. oldVNode：上一个虚拟节点（更新钩子函数中才有用）

**（2）使用场景**

- 普通DOM元素进行底层操作的时候，可以使用自定义指令
- 自定义指令是用来操作DOM的。尽管Vue推崇数据驱动视图的理念，但并非所有情况都适合数据驱动。自定义指令就是一种有效的补充和扩展，不仅可用于定义任何的DOM操作，并且是可复用的。

**（3）使用案例**

初级应用：

- 鼠标聚焦
- 下拉菜单
- 相对时间转换
- 滚动动画

高级应用：

- 自定义指令实现图片懒加载
- 自定义指令集成第三方插件

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.21 |
