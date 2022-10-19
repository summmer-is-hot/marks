## <font color=SlateBlue>前端常见面试题(Vue篇)（一）</font>

## 1. Vue的基本原理

当一个Vue实例创建时，Vue会遍历data中的属性，用 **Object.defineProperty**（vue3.0使用proxy ）将它们转为 getter/setter，并且在内部追踪相关依赖，在属性被访问和修改时通知变化。 每个组件实例都有相应的 **watcher 程序实例**，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。

![1.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/1.webp?)

### 1.2 Vue的优点

- 轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十 `kb` ；
- 简单易学：国人开发，中文文档，不存在语言障碍 ，易于理解和学习；
- 双向数据绑定：保留了 `angular` 的特点，在数据操作方面更为简单；
- 组件化：保留了 `react` 的优点，实现了 `html` 的封装和重用，在构建单页面应用方面有着独特的优势；
- 视图，数据，结构分离：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作；
- 虚拟DOM：`dom` 操作是非常耗费性能的，不再使用原生的 `dom` 操作节点，极大解放 `dom` 操作，但具体操作的还是 `dom` 不过是换了另一种方式；
- 运行速度更快：相比较于 `react` 而言，同样是操作虚拟 `dom`，就性能而言， `vue` 存在很大的优势。

## 2. Vue响应式的原理

### **2.1 什么是数据劫持**

数据劫持比较好理解，通常我们利用`Object.defineProperty`劫持对象的访问器，在属性值发生变化时我们可以获取变化，从而进行进一步操作。

### **2.2 发布者模式 / 订阅者模式**

在软件架构中，**发布订阅**是一种消息范式，消息的发送者（称为发布者）不会将消息直接发送给特定的接收者（称为订阅者）。而是将发布的消息分为不同的类别，无需了解哪些订阅者（如果有的话）可能存在。同样的，订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解哪些发布者（如果有的话）存在。

这里很明显了，区别就在于，不同于观察者和被观察者，**发布者和订阅者是互相不知道对方的存在的，发布者只需要把消息发送到订阅器里面，订阅者只管接受自己需要订阅的内容**

### **2.3 响应式原理**

Vue响应式的原理就是采用**数据劫持**结合**发布者-订阅者模式**的方式，通过**Object.defineProperty()** 来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。主要分为以下几个步骤：

**Observe**(被劫持的数据对象) **Compile**(vue的编译器) **Watcher**(订阅者) **Dep**(用于收集Watcher订阅者们)

1.需要给**Observe**的数据对象进行递归遍历，包括子属性对象的属性，都加上**setter**和**getter**这样的属性，给这个对象的某个值赋值，就会触发**setter**，那么就能监听到了数据变化。

2.**Compile**解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

3.**Watcher**订阅者是**Observer**和**Compile**之间通信的桥梁，主要做的事情是: ①在自身实例化时往属性订阅器(**Dep**)里面添加自己 ②自身必须有一个**update**()方法 ③待属性变动**dep.notice()** 通知时，能调用自身的**update()** 方法，并触发**Compile**中绑定的回调，则功成身退。

4.MVVM作为数据绑定的入口，整合**Observer**、**Compile**和**Watcher**三者，通过**Observer**来监听自己的**model**数据变化，通过**Compile**来解析编译模板指令，最终利用**Watcher**搭起**Observer**和**Compile**之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

![2.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/2.webp?)

## 3. Object.defineProperty的使用方式，有什么缺点

**使用方法**

**Object.defineProperty( obj, prop, descriptor )**

三个参数:

**obj** 要定义的对象

**prop** 要定义或修改的属性名称或 Symbol

**descriptor** 要定义或修改的属性描述符(配置对象)

属性描述符(配置对象)

**get** 属性的 getter 函数，如果没有 getter，则为 `undefined`。当访问该属性时，会调用此函数。执行时不传入任何参数，但是会传入 `this` 对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。该函数的返回值会被用作属性的值。 **默认为 [`undefined`]** **set** 属性的 setter 函数，如果没有 setter，则为 `undefined`。当属性值被修改时，会调用此函数。该方法接受一个参数（也就是被赋予的新值），会传入赋值时的 `this` 对象。 **默认为 [`undefined`]**

**缺点：**

在对一些属性进行操作时，使用这种方法无法拦截，比如**通过下标方式修改数组**数据或者**给对象新增属性**，这都不能触发组件的重新渲染，因为 Object.defineProperty 不能拦截到这些操作。更精确的来说，对于数组而言，大部分操作都是拦截不到的，只是 Vue 内部通过重写函数的方式解决了这个问题。

在 Vue3.0 中已经不使用这种方式了，而是通过使用 Proxy 对对象进行代理，从而实现数据劫持。使用Proxy 的好处是它可以完美的监听到任何方式的数据改变，唯一的缺点是兼容性的问题，因为 Proxy 是 ES6 的语法。

### 3.2 Object.defineProperty(target, key, options)，options可传什么参数？

- value：给target[key]设置初始值
- get：调用target[key]时触发
- set：设置target[key]时触发
- writable：规定target[key]是否可被重写，默认false
- enumerable：规定了key是否会出现在target的枚举属性中，默认为false
- configurable：规定了能否改变options，以及删除key属性，默认false，具体详细请看

## 4. MVVM、MVC、MVP的区别

### **（1）MVC**

**M: model数据模型, V:view视图模型, C: controller控制器**

MVC 通过分离 Model、View 和 Controller 的方式来组织代码结构。其中 View 负责页面的显示逻辑，Model 负责存储页面的业务数据，以及对相应数据的操作。并且 View 和 Model 应用了**观察者模式**，当 Model 层发生改变的时候它会**通知**有关 View 层更新页面。Controller 层是 View 层和 Model 层的**纽带**，它主要负责用户与应用的响应操作，当用户与页面产生交互的时候，Controller 中的事件触发器就开始工作了，通过调用 Model 层，来完成对 Model 的修改，然后 Model 层再去通知View视图更新。

![3.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/3.webp?)

### **（2）MVP**

**M: model数据模型, V:view视图模型, P: Presenter 控制器**

MVP 模式与 MVC 唯一不同的在于 Presenter 和 Controller。在 MVC 模式中使用观察者模式，来实现当 Model 层数据发生变化的时候，通知 View 层的更新。这样 View 层和 Model 层耦合在一起，当项目逻辑变得复杂的时候，可能会造成代码的混乱，并且可能会对代码的复用性造成一些问题。

MVP 的模式通过使用 Presenter 来实现对 View 层和 Model 层的**解耦**。MVC 中的Controller 只知道 Model 的接口，因此它没有办法控制 View 层的更新，MVP 模式中，View 层的接口暴露给了 Presenter 因此可以在 Presenter 中将 Model 的变化和 View 的变化**绑定**在一起，以此来实现 View 和 Model 的**同步更新**。这样就实现了对 View 和 Model 的解耦，Presenter 还包含了其他的响应逻辑。

### **（3）MVVM**

MVVM 分为 Model、View、ViewModel：

- **Model代表数据模型，数据和业务逻辑都在Model层中定义；**
- **View代表UI视图，负责数据的展示；**
- **ViewModel负责监听Model中数据的改变并且控制视图的更新，处理用户交互操作；**

Model和View并无直接关联，而是通过ViewModel来进行联系的，Model和ViewModel之间有着**双向数据绑定**的联系。因此当Model中的数据改变时会触发View层的刷新，View中由于用户交互操作而改变的数据也会在Model中同步。

这种模式实现了 Model和View的**数据自动同步**，因此开发者只需要专注于数据的维护操作即可，而不需要自己操作DOM。

![4.png](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/4.webp?)

### 4.2 **MVVM**的优缺点?

优点:

- 分离视图（View）和模型（Model），降低代码耦合，提⾼视图或者逻辑的重⽤性: ⽐如视图（View）可以独⽴于Model变化和修改，⼀个ViewModel可以绑定不同的"View"上，当View变化的时候Model不可以不变，当Model变化的时候View也可以不变。你可以把⼀些视图逻辑放在⼀个ViewModel⾥⾯，让很多view重⽤这段视图逻辑
- 提⾼可测试性: ViewModel的存在可以帮助开发者更好地编写测试代码
- ⾃动更新dom: 利⽤双向绑定,数据更新后视图⾃动更新,让开发者从繁琐的⼿动dom中解放

缺点:

- Bug很难被调试: 因为使⽤双向绑定的模式，当你看到界⾯异常了，有可能是你View的代码有Bug，也可能是Model的代码有问题。数据绑定使得⼀个位置的Bug被快速传递到别的位置，要定位原始出问题的地⽅就变得不那么容易了。另外，数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的
- ⼀个⼤的模块中model也会很⼤，虽然使⽤⽅便了也很容易保证了数据的⼀致性，当时⻓期持有，不释放内存就造成了花费更多的内存
- 对于⼤型的图形应⽤程序，视图状态较多，ViewModel的构建和维护的成本都会⽐较⾼

## 5. Vue的常用指令及作用

- v-on 给标签绑定函数，可以缩写为@，例如绑定一个点击函数 函数必须写在methods里面
- v-bind 动态绑定 作用： 及时对页面的数据进行更改, 可以简写成：冒号
- v-slot: 缩写为#, 组件插槽
- v-for 根据数组的个数, 循环数组元素的同时还生成所在的标签
- v-show 显示内容
- v-if 显示与隐藏
- v-else 必须和v-if连用 不能单独使用 否则报错
- v-text 解析文本
- v-html 解析html标签

### 5.2 Vue怎么动态绑定Class 与 Style

```markdown
v-bind:class="{{ '类名': bool, '类名': bool ......}}"
v-bind:class="{ '类名': bool, '类名': bool ......}"
如果值为true 该类样式就会被应用在元素身上, false则不会
注意点：如果类名有 - ，则需要使用引号包起来
```

`v-bind:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```vue
<div v-bind:style="styleObject"></div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

## 6. vue常用的修饰符

**v-on**

- `.stop` - 调用 `event.stopPropagation()`。 阻止默认事件
- `.prevent` - 调用 `event.preventDefault()`。阻止默认行为
- `.native` - 监听组件根元素的原生事件。

**v-bind**

- `.prop` - 作为一个 DOM property 绑定而不是作为 attribute 绑定。
- `.camel` - (2.1.0+) 将 kebab-case attribute 名转换为 camelCase。(从 2.1.0 开始支持)
- `.sync` (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 `v-on` 侦听器。

**v-model**

- [`.lazy`]- 取代 `input` 监听 `change` 事件
- [`.number`] - 输入字符串转为有效的数字
- [`.trim`] - 输入首尾空格过滤

## 7. Vue的内置组件

### **component**

渲染一个“元组件”为动态组件。依 is 的值，来决定哪个组件被渲染

在一个多标签的界面中使用 is attribute 来切换不同的组件：tap栏切换

### **transition**

用于在 Vue 插入、更新或者移除 DOM 时， 提供多种不同方式的应用过渡、动画效果。

### **transition-group**

`<transition-group>` 用于给列表统一设置过渡动画。

### **keep-alive**

- 主要用于保留组件状态或避免组件重新渲染。
- **include** 属性用于指定哪些组件会被缓存，具有多种设置方式。
- **exclude** 属性用于指定哪些组件不会被缓存。
- **max** 属性用于设置最大缓存个数。

### **slot**

- **name** - string，用于命名插槽。
- < slot> 元素作为组件模板之中的内容分发插槽。< slot> 元素自身将被替换。

## 8. v-if、v-show、v-html 的原理

`v-if`会调用addIfCondition方法，VNode，render的时候就不会渲染；

`v-show`会生成VNode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改show属性值，也就是常说的display；

`v-html`会先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值。

## 9. v-show和v-if的区别

- v-show和v-if的区别? 分别说明其使用场景?
- 相同点： v-show 和v-if都是true的时候显示，false的时候隐藏
- 不同点1：原理不同
    - `v-show`:一定会渲染，只是修改display属性
    - `v-if`:根据条件渲染
- 不同点2：应用场景不同
    - 频繁切换用v-show,不频繁切换用v-if

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建，操作的实际上是dom元素的创建或销毁。

`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换 它操作的是display:none/block属性。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

## 10. 为什么避免v-for和v-if在一起使用?

Vue 处理指令时，v-for 比 v-if 具有更高的`优先级`, 虽然用起来也没报错好使, 但是性能不高, 如果你有5个元素被v-for循环, v-if也会分别执行5次.

## 11. v-for 循环为什么一定要绑定key ?

> 提升vue渲染性能

- 1.vue在渲染的时候,会 先把 新DOM 与 旧DOM 进行对比， 如果dom结构一致，则vue会复用旧的dom。 （此时可能造成数据渲染异常）
- 2.使用key可以给dom添加一个标识符，让vue强制更新dom

比如有一个列表 li1 到 li4，我们需要在中间插入一个li3，`li1` 和 `li2` 不会重新渲染，而 `li3、li4、li5` 都会重新渲染

因为在不使用 `key` 或者列表的 `index` 作为 `key` 的时候，每个元素对应的位置关系都是 index，直接导致我们插入的元素到后面的全部元素，对应的位置关系都发生了变更，所以全部都会执行更新操作, 这是不可取的

而在使用唯一 `key` 的情况下，每个元素对应的位置关系就是 `key`，来看一下使用唯一 `key` 值的情况下

这样如图中的 `li3` 和 `li4` 就不会重新渲染，因为元素内容没发生改变，对应的位置关系也没有发生改变。

这也是为什么 v-for 必须要写 key，而且不建议开发中使用数组的 index 作为 key 的原因

总结一下：

- key 的作用主要是为了更高效的更新虚拟 DOM，因为它可以非常精确的找到相同节点，因此 patch 过程会非常高效
- Vue 在 patch 过程中会判断两个节点是不是相同节点时，key 是一个必要条件。比如渲染列表时，如果不写 key，Vue 在比较的时候，就可能会导致频繁更新元素，使整个 patch 过程比较低效，影响性能
- 应该避免使用数组下标作为 key，因为 key 值不是唯一的话可能会导致上面图中表示的 bug，使 Vue 无法区分它他，还有比如在使用相同标签元素过渡切换的时候，就会导致只替换其内部属性而不会触发过渡效果
- 从源码里可以知道，Vue 判断两个节点是否相同时主要判断两者的元素类型和 key 等，如果不设置 key，就可能永远认为这两个是相同节点，只能去做更新操作，就造成大量不必要的 DOM 更新操作，明显是不可取的

### 11.2 为什么不建议用index索引作为key?

使用index 作为 key和没写基本上没区别，因为不管数组的顺序怎么颠倒，index 都是 0, 1, 2...这样排列，导致 Vue 会复用错误的旧子节点，做很多额外的工作。

## 12. v-model 是如何实现的，语法糖实际是什么？

**（1）作用在表单元素上** 动态绑定了 input 的 value 指向了 message 变量，并且在触发 input 事件的时候去动态把 message设置为目标值：

![5.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/5.webp?)

**（2）作用在组件上** 在自定义组件中，v-model 默认会利用名为 value 的 prop和名为 input 的事件

**本质是一个父子组件通信的语法糖，通过prop和$.emit实现。** 因此父组件 v-model 语法糖本质上可以修改为：

![6.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/6.webp?)

在组件的实现中，可以通过 v-model属性来配置子组件接收的prop名称，以及派发的事件名称。 例子：

![7.jpg](https://raw.githubusercontent.com/summmer-is-hot/marks/main/assets/vue/7.webp?)

默认情况下，一个组件上的v-model 会把 value 用作 `prop`且把 input 用作 `event`。但是一些输入类型比如单选框和复选框按钮可能想使用 value prop 来达到不同的目的。使用 model 选项可以回避这些情况产生的冲突。js 监听input 输入框输入数据改变，用oninput，数据改变以后就会立刻出发这个事件。通过input事件把数据`$emit` 出去，在父组件接受。父组件设置v-model的值为input `$emit`过来的值。

## 结语

> &emsp; 今天的面试题的分享就到这边啦，明天继续给大家分享~

## 作者

| Coder       | 小红书ID  | 创建时间   |
| :---------- | :-------- | :--------- |
| 落寞的前端👣 | 121450513 | 2022.10.19 |
