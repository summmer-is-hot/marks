## <font color=SlateBlue>前端常见面试题(Vue篇)（二）</font>

## 13. v-model 可以被用在自定义组件上吗？如果可以，如何使用？

**可以**。v-model 实际上是一个语法糖，用在自定义组件上也是同理：

![1.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_1.webp?)

相当于

![2.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_2.webp?)

显然，custom-input 与父组件的交互如下：

1. 父组件将`searchText`变量传入custom-input 组件，使用的 prop 名为`value`；
2. custom-input 组件向父组件传出名为`input`的事件，父组件将接收到的值赋值给`searchText`；

所以，custom-input 组件的实现应该类似于这样：

![3.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_3.webp?)

## 14. v-model和.sync的对比

v-model与.sync的共同点：都是`语法糖`，都可以实现父子组件中的数据的双向通信。

v-model与.sync的不共同点：

**v-model：**

1.父组件 v-model="" 子组件 @(input,value)

2.一个组件只能绑定`一个`v-model

3.v-model针对更多的是最终操作结果，是`双向绑定的结果`，是`value`，是一种`change操作`。

**.sync：**

1.父组件 :my-prop-name.sync 子组件@update:my-prop-name 的模式来替代事件触发，实现父子组件间的双向绑定。

2.一个组件可以`多个`属性用.sync修饰符，可以同时"双向绑定多个“prop”

3..sync针对更多的是各种各样的状态，是`状态的互相传递`，是`status`，是一种`update操作`。

## 15. 计算属性computed 和watch 的区别是什么？

#### **`Computed`：**

- 它**支持缓存**，只有依赖的数据发生了变化，才会重新计算
- **不支持异步**，当Computed中有异步操作时，无法监听数据的变化
- computed的值会**默认走缓存**，计算属性是基于它们的响应式依赖进行缓存的，也就是基于data声明过，或者父组件传递过来的props中的数据进行计算的。
- **如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，一般会使用computed**
- 如果computed属性的属性值是函数，那么**默认使用get方法**，函数的返回值就是属性的属性值；在computed中，属性有一个get方法和一个set方法，当数据发生变化时，会调用set方法。

#### **`Watch`：**

- 它**不支持缓存**，数据变化时，它就会触发相应的操作
- **支持异步**监听
- 监听的函数接收**两个参数**，第一个参数是最新的值，第二个是变化之前的值
- 当一个属性发生变化时，就需要执行相应的操作
- 监听数据必须是data中声明的或者父组件传递过来的props中的数据，当发生变化时，会触发其他操作，函数有两个的参数：
    - **immediate**：组件加载立即触发回调函数
    - **deep**：深度监听，发现数据内部的变化，在复杂数据类型中使用，例如数组中的对象发生变化。需要注意的是，deep无法监听到数组和对象内部的变化。

当想要执行异步或者昂贵的操作以响应不断的变化时，就需要使用watch。

**总结：**

- `computed` 计算属性 : 依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值。
- `watch` 侦听器 : 更多的是**观察**的作用，**无缓存性**，类似于某些数据的监听回调，每当监听的数据变化时都会执行回调进行后续操作。

**运用场景：**

- 当需要进行数值计算,并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时都要重新计算。
- 当需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许执行异步操作 ( 访问一个 API )，限制执行该操作的频率，并在得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

## 16. Computed 和 Methods 的区别

可以将同一函数定义为一个 method 或者一个计算属性。对于最终的结果，两种方式是相同的

**不同点：**

- `computed`: 计算属性是基于它们的依赖进行缓存的，只有在它的相关依赖发生改变时才会重新求值；
- `method` 调用总会执行该函数。

## 17. 什么是组件

**组件就是把图形、非图形的各种逻辑均抽象为一个`统一的概念`（组件）来实现开发的模式，在`Vue`中每一个`.vue`文件都可以视为一个组件**

#### **组件的优势**

1. 降低整个系统的耦合度，在保持接口不变的情况下，我们可以替换不同的组件快速完成需求
2. 调试方便，由于整个系统是通过组件组合起来的，在出现问题的时候，可以用排除法直接移除组件，或者根据报错的组件快速定位问题，之所以能够快速定位，是因为每个组件之间低耦合，职责单一，所以逻辑会比分析整个系统要简单
3. 提高可维护性，由于每个组件的职责单一，并且组件在系统中是被复用的，所以对代码进行优化可获得系统的整体升级

## 18. 什么是插件

**插件通常用来为 `Vue` 添加全局功能**。插件的功能范围没有严格的限制——一般有下面几种：

- 添加全局方法或者属性。如: `vue-custom-element`
- 添加全局资源：指令/过滤器/过渡等。如 `vue-touch`
- 添加全局公共组件 Vue.component()
- 添加全局公共指令 Vue.directive()
- 通过全局混入来添加一些组件选项。如`vue-router`
- 添加 `Vue` 实例方法，通过把它们添加到 `Vue.prototype` 上实现。
- 一个库，提供自己的 `API`，同时提供上面提到的一个或多个功能。如`vue-router`

### 18.2 Vue2和Vue3怎么注册全局组件

Vue2使用
```js
//main.js
import Vue from 'vue'
import header from "@/components/header.vue"
Vue.component("header", header)
```

Vue3使用
```js
//main.js
import  { createApp } from 'vue'
import App from './App.vue'
import header from "@/components/header.vue"

const app = createApp(App)
app.component('header', header )

```

```js
const app = createApp(App)
app.component('组件名','组件对象')
```

### 18.3 Vue2、Vue3怎么封装自定义插件并使用/ Vue.use() （install）

**Vue2**

在components.index.js里，定义一个函数或对象，在里面可以使用Vue.component全局注册组件，并暴露出去

在main.js里使用Vue.use( )，参数类型必须是 object 或 Function

**Vue3**

在components.index.ts里，定义一个函数或对象，在里面可以使用app.component全局注册组件，并暴露出去

在main.ts里使用app.use( )，参数类型必须是 object 或 Function

------

如果是 Function 那么这个函数就被当做 install 方法

如果是 object 则需要定义一个 install 方法

## 19. 组件通信/ 组件传值的方法

**什么是组件通信**

组件(`.vue`)通过某种方式来传递信息以达到某个目的

**组件通信解决了什么问题**

每个组件之间的都有独自的作用域，组件间的数据是无法共享的但实际开发工作中我们常常需要让组件之间共享数据，这也是组件通信的目的要让它们互相之间能进行通讯，这样才能构成完整系统

### **（1）props / $emit (父子)**

父组件通过`props`向子组件传递数据，子组件通过`$emit`和父组件通信

**1. 父组件向子组件传值**

`props`只能是父组件向子组件进行传值，`props`使得父子组件之间形成了一个单向下行绑定。子组件的数据会随着父组件不断更新。

`props` 可以显示定义一个或一个以上的数据，对于接收的数据，可以是各种数据类型，同样也可以传递一个函数。

`props`属性名规则：若在`props`中使用驼峰形式，模板中需要使用短横线的形式

![4.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_4.webp?)

![5.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_5.webp?)

**2. 子组件向父组件传值**

- `$emit`绑定一个自定义事件，当这个事件被执行的时就会将参数传递给父组件，而父组件通过`v-on`监听并接收参数。

### （2）依赖注入 provide / inject（父子、祖孙）

这种方式就是Vue中的**依赖注入**，该方法用于**父子组件之间的通信**。当然这里所说的父子不一定是真正的父子，也可以是祖孙组件，在层数很深的情况下，可以使用这种方法来进行传值。就不用一层一层的传递了。

`provide / inject`是Vue提供的两个钩子，和`data`、`methods`是同级的。并且`provide`的书写形式和`data`一样。

- `provide` 钩子用来发送数据或方法
- `inject`钩子用来接收数据或方法

在父组件中：

![6.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_6.webp?)

在子组件中：

![7.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_7.webp?)

还可以这样写，这样写就可以访问父组件中的所有属性：

![8.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_8.webp?)

**注意：** 依赖注入所提供的属性是**非响应式**的。

### （3）ref / $refs （父子，兄弟）

`ref`： 这个属性用在子组件上，它的引用就指向了子组件的实例。可以通过实例来访问组件的数据和方法。

这种方式也是实现**兄弟组件**之间的通信。子组件1通过**this.`$emit`\**通知父组件调用函数，父组件的函数里用\**this.$refs**拿到子组件2的方法，这样就实现兄弟组件之间的通信。

在子组件中：

![9.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_9.webp?)

在父组件中：

![10.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_10.webp?)

### （4）`$parent` / `$children` (父子)

- 使用`$parent`可以让组件访问父组件的实例（访问的是上一级父组件的属性和方法）
- 使用`$children`可以让组件访问子组件的实例，但是，`$children`并不能保证顺序，并且访问的数据也不是响应式的。

在子组件中：

![11.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_11.webp?)

在父组件中：

![12.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_12.webp?)

在上面的代码中，子组件获取到了父组件的`parentVal`值，父组件改变了子组件中`message`的值。 **需要注意：**

- 通过`$parent`访问到的是上一级父组件的实例，可以使用`$root`来访问根组件的实例
- 在组件中使用`$children`拿到的是所有的子组件的实例，它是一个数组，并且是无序的
- 在根组件`#app`上拿`$parent`得到的是`new Vue()`的实例，在这实例上再拿`$parent`得到的是`undefined`，而在最底层的子组件拿`$children`是个空数组
- `$children` 的值是**数组**，而`$parent`是个**对象**

### （5）`$attrs` / `$listeners` (祖孙)

考虑一种场景，如果A是B组件的父组件，B是C组件的父组件。如果想要组件A给组件C传递数据，这种隔代的数据，该使用哪种方式呢？

如果是用`props/$emit`来一级一级的传递，确实可以完成，但是比较复杂；如果使用事件总线，在多人开发或者项目较大的时候，维护起来很麻烦；如果使用Vuex，的确也可以，但是如果仅仅是传递数据，那可能就有点浪费了。

针对上述情况，Vue引入了`$attrs / $listeners`，实现组件之间的跨代通信。

先来看一下`inheritAttrs`，它的默认值true，继承所有的父组件属性除`props`之外的所有属性；`inheritAttrs：false` 只继承class属性 。

- `$attrs`：继承所有的父组件属性（除了prop传递的属性、class 和 style ），一般用在子组件的子元素上
- `$listeners`：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。（相当于子组件继承父组件的事件）

A组件（`APP.vue`）：

![13.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_13.webp?)

B组件（`Child1.vue`）：

![14.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_14.webp?)

C 组件 (`Child2.vue`)：

![15.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_15.webp?)

在上述代码中：

- C组件中能直接触发test的原因在于 B组件调用C组件时 使用 v-on 绑定了`$listeners` 属性
- 在B组件中通过v-bind 绑定`$attrs`属性，C组件可以直接获取到A组件中传递下来的props（除了B组件中props声明的）

### （6）eventBus事件总线（`$emit / $on`）（任意组件通信）

`eventBus`事件总线适用于**父子组件**、**非父子组件**等之间的通信，使用步骤如下：

**（1）创建事件中心管理组件之间的通信**

![16.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_16.webp?)

**（2）发送事件** 假设有两个兄弟组件`firstCom`和`secondCom`：

![17.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_17.webp?)

在`firstCom`组件中发送事件：

![18.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_18.webp?)

**（3）接收事件** 在`secondCom`组件中接收事件：

![19.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2_19.webp?)

在上述代码中，这就相当于将`num`值存贮在了事件总线中，在其他组件中可以直接访问。事件总线就相当于一个桥梁，不用组件通过它来通信。

虽然看起来比较简单，但是这种方法也有不变之处，如果项目过大，使用这种方式进行通信，后期维护起来会很困难。

### （7）总结

**（1）父子组件间通信**

- 子组件通过 props 属性来接受父组件的数据，然后父组件在子组件上注册监听事件，子组件通过 emit 触发事件来向父组件发送数据。
- 通过 ref 属性给子组件设置一个名字。父组件通过 `$refs` 组件名来获得子组件，子组件通过 `$parent` 获得父组件，这样也可以实现通信。
- 使用 provide/inject，在父组件中通过 provide提供变量，在子组件中通过 inject 来将变量注入到组件中。不论子组件有多深，只要调用了 inject 那么就可以注入 provide中的数据。

**（2）兄弟组件间通信**

- 使用 eventBus 的方法，它的本质是通过创建一个空的 Vue 实例来作为消息传递的对象，通信的组件引入这个实例，通信的组件通过在这个实例上监听和触发事件，来实现消息的传递。
- 通过 `$parent/$refs` 来获取到兄弟组件，也可以进行通信。

**（3）任意组件之间**

- 使用 eventBus ，其实就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。

如果业务逻辑复杂，很多组件之间需要同时处理一些公共的数据，这个时候采用上面这一些方法可能不利于项目的维护。这个时候可以使用 vuex ，vuex 的思想就是将这一些公共的数据抽离出来，将它作为一个全局的变量来管理，然后其他组件就可以对这个公共数据进行读写操作，这样达到了解耦的目的。


## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.20 |
