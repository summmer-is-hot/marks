## <font color=SlateBlue>前端常见面试题(Vue篇)（六）</font>

## Vuex

## 1. 什么Vuex ,谈谈你对它的理解？

1. 首先vuex的出现是为了解决web组件化开发的过程中，各组件之间传值的复杂和混乱的问题
2. 将我们在多个组件中需要共享的数据放到state中，
3. 要获取或格式化数据需要使用getters，
4. 改变state中的数据，可以使用mutation，但是只能包含同步的操作，在具体组件里面调用的方式`this.$store.commit('xxxx')`
5. Action也是改变state中的数据，不过是提交的mutation，并且可以包含异步操作，在组件中的调用方式`this.$store.dispatch('xxx')`； 在actions里面使用的commit('调用mutation')

**Vuex** 是一个专为 Vue.js应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 devtools extension，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

## 2. Vuex**各模块在核心流程中的主要功能：**

`Vue Components`∶ Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。

`dispatch`∶操作行为触发方法，是唯一能执行action的方法。

`actions`∶ 操作行为处理模块。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。

`commit`∶状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。

`mutations`∶状态改变操作方法。是Vuex修改state的唯一推荐方法，其他修改方式在严格模式下将会报错。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。

`state`∶ 页面状态管理容器对象。集中存储VueComponents中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。

`getters`∶ state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。

## 2.1 简述Vuex的数据传输流程

当组件进行数据修改的时候我们需要调**用dispatch来触发actions里面的方法**。actions里面的每个方法中都会有一个commit方法，当方法执行的时候会通过**commit来触发mutations里面的方法进行数据的修改**。mutations里面的每个函数都会有一个state参数，这样就可以在**mutations里面进行state的数据修改**，当数据修改完毕后，会传导给页面。页面的数据也会发生改变

## 3. vuex中有几个核心属性，分别是什么？

一共有5个核心属性，分别是:

- `state`唯一数据源,Vue 实例中的 data 遵循相同的规则
- `mutation`更改 Vuex 的 store 中的状态的唯一方法是提交 mutation,非常类似于事件,通过store.commit 方法触发
- `action`action 类似于 mutation，不同在于action 提交的是 mutation，而不是直接变更状态，action 可以包含任意异步操作
- `module` 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

- `getters` 可以认为是 store 的计算属性,就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。Getter 会暴露为 store.getters 对象，你可以以属性的形式访问这些值.

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})

store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

## 4. Vuex中action和mutation的区别

`mutation`中的操作是一系列的同步函数，用于修改state中的变量的的状态。当使用vuex时需要通过commit来提交需要操作的内容。mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

![1.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_1.webp?)

当触发一个类型为 increment 的 mutation 时，需要调用此函数：

![2.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_2.webp?)

而`action`类似于mutation，不同点在于：

- action 可以包含任意异步操作。
- action 提交的是 mutation，而不是直接变更状态。

![3.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_3.webp?)

`action`函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。 所以，两者的不同点如下：

- mutation专注于修改State，理论上是修改State的唯一途径；action 用来处理业务代码、异步请求。
- mutation：必须同步执行；action ：可以异步，但不能直接操作State。
- 在视图更新时，先触发actions，actions再触发mutation
- mutation的参数是state，它包含store中的数据；store的参数是context，它是 state 的父级，包含 state、getters

## 5. vuex的getter的作用

`getter`有点类似 Vue.js 的**计算属性**，当我们需要从 store 的 state 中派生出一些状态，那么我们就需要使用 getter，getter 会接收 state 作为第 一个参数，而且 getter 的返回值会根据它的依赖被缓存起来，只有 getter 中的依赖值（state 中的某个需要派生状态的值）发生改变的时候才会被重新计算。

## 6. Vuex 和 localStorage 的区别

**（1）** **最重要的区别**

- vuex存储在**内存**中
- localstorage 则以**文件**的方式存储在**本地**，只能存储字符串类型的数据，存储对象需要 JSON的stringify和parse方法进行处理。 读取内存比读取硬盘速度要快

**（2）应用场景**

- Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。vuex用于组件之间的传值。
- localstorage是本地存储，是将数据存储到浏览器的方法，一般是在跨页面传递数据时使用 。
- Vuex能做到数据的响应式，localstorage不能

**（3）永久性**

**刷新页面时vuex存储的值会丢失，localstorage不会。**

**注意：** 对于不变的数据确实可以用localstorage可以代替vuex，但是当两个组件共用一个数据源（对象或数组）时，如果其中一个组件改变了该数据源，希望另一个组件响应该变化时，localstorage无法做到，原因就是上面的最重要的区别。

## 7. Vuex页面刷新时丢失怎么处理

用sessionStorage 或者 localstorage 存储数据

存储： sessionStorage.setItem( '名', JSON.stringify(值) ) 使用： sessionStorage.getItem('名') ---得到的值为字符串类型，用JSON.parse()去引号；

## 8. Vuex和单纯的全局对象有什么区别？

- Vuex 的状态存储是**响应式**的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- 不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样可以方便地跟踪每一个状态的变化，从而能够实现一些工具帮助更好地了解我们的应用。

## 9. Redux(react的) 和 Vuex 有什么区别，它们的共同思想

**（1）Redux 和 Vuex区别**

- Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值即可
- Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的State即可
- Vuex数据流的顺序是∶View调用store.commit提交对应的请求到Store中对应的mutation函数->store改变（vue检测到数据变化自动渲染）

通俗点理解就是，vuex 弱化 dispatch，通过commit进行 store状态的一次更变;取消了action概念，不必传入特定的 action形式进行指定变更;弱化reducer，基于commit参数直接对数据进行转变，使得框架更加简易;

**（2）共同思想**

- 单—的数据源
- 变化可以预测

本质上：redux与vuex都是对mvvm思想的服务，将数据从视图中抽离的一种方案; 形式上：vuex借鉴了redux，将store作为全局的数据中心，进行mode管理;

## 10. 为什么要用 Vuex 或者 Redux

由于传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致代码无法维护。

所以需要把组件的**共享**状态抽取出来，以一个全局单例模式管理。在这种模式下，组件树构成了一个巨大的"视图"，不管在树的哪个位置，任何组件都能获取状态或者触发行为。

另外，通过定义和隔离状态管理中的各种概念并强制遵守一定的规则，代码将会变得更结构化且易维护。

## 11. 为什么 Vuex 的 mutation 中不能做异步操作？

- Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样可以方便地跟踪每一个状态的变化，从而能够实现一些工具帮助更好地了解我们的应用。
- 每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。

## 12. Vuex的严格模式是什么,有什么作用，如何开启？

在严格模式下，无论何时发生了状态变更且不是由mutation函数引起的，将会抛出错误。这能保证所有的状态变更都能被调试工具跟踪到。

在Vuex.Store 构造器选项中开启,如下

![4.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_4.webp?)

## 13. 如何在组件中批量使用Vuex的getter属性

使用`mapGetters`辅助函数, 利用对象展开运算符将getter混入computed 对象中

![5.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_5.webp?)

## 14. 如何在组件中重复使用Vuex的mutation

使用`mapMutations`辅助函数,在组件中这么使用

![6.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6_6.webp?)

然后调用`this.setNumber(10)`相当调用`this.$store.commit('SET_NUMBER',10)`

## 15. Vuex的辅助函数怎么用

比如当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState`辅助函数帮助我们生成计算属性，让你少按几次键。

### mapState

```javascript
import { mapState } from 'vuex'
 
export default {
  // ...
  computed:{
     ...mapState({
         // 箭头函数可使代码更简练
         count: state => state.count,
         // 传字符串参数 'count' 等同于 `state => state.count`
         countAlias: 'count',
 
         // 为了能够使用 `this` 获取局部状态，必须使用常规函数
         countPlusLocalState (state) {
             return state.count + this.localCount
         }
  	})
  }
}
```

定义的属性名与state中的名称相同时，可以传入一个数组

```vue
//定义state
const state={
    count:1,
}
 
//在组件中使用辅助函数
computed:{
    ...mapState(['count'])
}
```

### mapGetters

```vue
computed:{
    ...mapGetters({
      // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
      doneCount: 'doneTodosCount'
    })
}
```

当属性名与getters中定义的相同时，可以传入一个数组

总结：

- mapState与mapGetters都用computed来进行映射
- 在组件中映射完成后，通过this.映射属性名进行使用

### mapMutations

```vue
methods:{
    ...mapMutations({
        add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
}
```

当属性名与mapMutations中定义的相同时，可以传入一个数组

```vue
methods:{
    ...mapMutations([
        'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
 
        // `mapMutations` 也支持载荷：
        'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
}
```

### mapActions

```vue
methods:{
    ...mapActions({
        add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
}
```

当属性名与mapActions中定义的相同时，可以传入一个数组

```vue
methods:{
    ...mapActions([
        'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`	
        // `mapActions` 也支持载荷：
        'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
}
```

总结

- mapMutations与mapActions都在methods中进行映射
- 映射之后变成一个方法

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.24 |
