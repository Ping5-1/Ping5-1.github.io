## 一、设计细节及原理
### 1. 双向绑定
#### 1.1 v-model 的原理
[前端面试题宝典-v-model 的原理是什么样的？](https://fe.ecool.fun/topic/e7f68d8b-d130-415e-8f9d-a47deeb462f9)
> v-model是Vue.js框架中的一个指令，用于在表单元素和组件之间实现双向数据绑定。
原理：**通过监听表单元素的输入事件（如input或change），将用户的输入同步到Vue实例中的属性，并在属性值变化时重新渲染视图**。
>①Vue解析模板时，会将v-model指令转换成合适的属性和事件绑定。对于大多数表单元素（如input、select、textarea等），它会将`value`属性与输入框的当前值进行绑定，并添加相应的事件监听器监听`input`或`change`事件，根据用户输入实时更新绑定的数据。
②当用户在输入框中键入或选择内容时，触发事件，Vue会捕获该事件并更新绑定的数据，根据数据的变化重新渲染视图。
注：如果在表单元素上使用`v-model`的`lazy`修饰符，Vue会监听`change`事件而不是`input`事件。只有当用户完成输入并触发change事件时，才会更新绑定的数据。
#### 1.2 谈谈对Vue中双向绑定的理解
[前端面试题宝典-谈谈对Vue中双向绑定的理解](https://fe.ecool.fun/topic/da1881cc-64b9-40f8-8a25-a6256c1349ed)
#### 1.3 手写实现双向绑定
==TODO==
#### 1.4 Vue 中的双向绑定和单向数据流原则是否冲突？
==TODO==

### 2. 样式隔离
样式隔离：每个组件的样式都被限制在自己的作用域内，不会影响其他组件或全局样式。这种方式实现了组件级别的样式隔离，使得组件可以更好地封装和重用，同时减少了样式冲突的可能性。
#### 2.1 vue 中怎么实现样式隔离？
[前端面试题宝典-vue 中怎么实现样式隔离？](https://fe.ecool.fun/topic/d23c0c59-8096-4636-8c77-e74279986a18)
> ①**作用域样式（Scoped Styles）**：在 Vue 单文件组件中，可以使用 scoped 特性将样式限定于当前组件的作用域。使用`<style scoped>`标签包裹的样式只对当前组件起作用，不会影响其他组件或全局样式。Vue 实现作用域样式的方式是通过给每个选择器添加一个唯一的属性选择器，以确保样式仅适用于当前组件。
②**CSS Modules**：Vue 支持使用 CSS Modules 来实现样式的模块化和隔离。
在 Vue 单文件组件中，可以借助 module 特性启用 CSS Modules 功能，在样式文件中使用类似 :local(.className) 的语法来定义局部样式。CSS Modules 会自动生成唯一的类名，并在编译时将类名与元素关联起来，从而实现样式的隔离和局部作用域。
③**CSS-in-JS**：Vue 也可以结合第三方 CSS-in-JS 库（如 styled-components、emotion 等）来实现样式的隔离。使用这种方式，可以直接在组件代码中编写样式，并通过 JavaScript 对象或模板字符串的形式动态生成样式。
CSS-in-JS 方案将样式与组件紧密关联，实现了更高程度的样式隔离和可重用性。
#### 2.2 说说 Vue 中 CSS scoped 的原理
[前端面试题宝典-说说 Vue 中 CSS scoped 的原理](https://fe.ecool.fun/topic/3e12b5bf-53ed-4b71-a199-49d7935f87b4)
==TOREAD==

[前端面试题宝典-Scoped Styles 为什么可以实现样式隔离？](https://fe.ecool.fun/topic/bcfc6fa0-1f1c-4aa4-b412-fd305db93c58)
> ①**唯一选择器**：当 Vue 编译单文件组件时，在样式中使用 scoped 特性或 module 特性时，Vue 会<u>___为每个样式选择器生成一个唯一类似于`[data-v-xxxxxxx]`的属性选择器___</u>，其中 xxxxxxx 是一个唯一的标识符。
②**编译时转换**：Vue 在编译过程中会解析单文件组件的模板，并对样式进行处理。
对于具有 scoped 特性的样式，Vue 会将选择器转换为带有唯一属性选择器的形式，例如 .class 会被转换为 .class[data-v-hash:8]。
对于具有 module 特性的样式，Vue 会为每个选择器生成一个唯一的类名，并将类名与元素关联起来。
③**渲染时应用**：在组件渲染过程中，Vue 会为组件的根元素添加一个属性值为唯一标识符的属性，例如 data-v-xxxxxxx。
当组件渲染完成后，样式选择器中的唯一属性选择器或唯一类名将与组件根元素的属性匹配，从而实现样式的隔离。
这样，只有具有相同属性值的元素才会应用相应的样式，避免了样式冲突和泄漏。
#### 2.3 如何打破 scope 对样式隔离的限制？
[前端面试题宝典-如何打破 scope 对样式隔离的限制](https://fe.ecool.fun/topic/68243618-bcf1-4cfa-a5c1-544dee6ac436)
> ①**使用`/deep/` 或 `::v-deep`，`:deep()`**
②**使用全局样式**： `<style>` 标签外部或使用 @import 引入全局样式文件，这样样式将不受作用域限制。
③**使用类名继承**：在子组件的 `<style>` 标签中使用 `@extend` 来继承父组件或其他组件的样式，这样可以打破作用域限制。

### 3. computed与watch
#### computed怎么实现的缓存
#### computed 计算值为什么还可以依赖另外一个 computed 计算值？
#### vue中computed和watch区别 

### 4. 事件
#### vue 文件中，在 v-for 时给每项元素绑定事件需要用事件代理吗，为什么？
#### 谈谈 Vue 事件机制，并手写$on、$off、$emit、$once
==TODO==

### 5. 其他
#### Vue 中的 h 函数有什么用？
==TODO==
#### 怎么在 Vue 中定义全局方法？
#### vue组件里写的原生addEventListeners监听事件，要手动去销毁吗？为什么？
#### Vue常用的修饰符有哪些？分别有什么应用场景？
#### Vue.observable是什么？
#### 为什么Vue中的data属性是一个函数而不是一个对象？
#### Vue2 动态给 data 添加一个新的属性时会发生什么？
==TODO==

## 二、Vue3 新特性
#### Vue3跟Vue2的区别？
#### vue3 相比较于 vue2，在编译阶段有哪些改进？
#### Vue 3.0中Treeshaking特性是什么，并举例进行说明？
#### 如果使用Vue3.0实现一个 Modal，你会怎么进行设计？
#### vue3 为什么要引入 Composition API？
#### Vue3.0 所采用的 Composition Api 与 Vue2.x 使用的 Options Api 有什么不同？
#### 怎么理解 Vue3 提供的 markRaw ？
==TODO==
#### vue3中怎么设置全局变量？
#### vue3 为什么不需要时间分片？
#### Vue中的 ref、toRef 和 toRefs 有什么区别？
==TODO==

## 三、渲染
#### 说说Vue页面渲染流程
### 1. 生命周期
#### vue中，推荐在哪个生命周期发起请求？
#### 说说你对Vue生命周期的理解
#### Vue实例挂载的过程中发生了什么？
#### Vue中，created和mounted两个钩子之间调用时间差值受什么影响？
#### created 和 mounted 有什么区别？
#### Vue 中父组件怎么监听到子组件的生命周期？

### 2.响应式

#### 2.1 Vue 数据响应式与diff
[前端面试题宝典-Vue 有了数据响应式，为何还要 diff？](https://fe.ecool.fun/topic/cd026df0-0b62-47dc-93e3-c30a1c3d8ab5)
> Vue 中的数据响应式和虚拟 DOM 的 diff 算法是两个不同的概念，它们分别解决了不同的问题，相互协作以提高页面渲染的效率和性能。
**数据响应式：保证数据和视图的同步更新，提供便捷的开发方式。
虚拟 DOM + Diff 算法：提高页面渲染的效率和性能，减少不必要的 DOM 操作，确保页面的流畅性和响应性**

> **数据响应式系统**通过`Object.defineProperty` 或者 ES6 的 `Proxy` 来实现，主要解决了：
①**数据绑定**：保证了视图与数据的同步更新，当数据发生变化时，视图会自动更新，避免了手动操作 DOM 的繁琐和易出错性。
②**依赖追踪**：Vue 能够追踪每个数据的依赖关系，即哪些组件或者计算属性依赖于某个数据。当数据变化时，自动更新依赖的组件或者计算属性。

> **虚拟 DOM** 是一种内存中的表示结构，是对真实 DOM 的抽象。Diff 算法是一种高效更新 DOM 的策略，它通过比较新旧虚拟 DOM 树的差异，最小化了更新操作，提高了页面的渲染效率。主要解决了：
①**性能优化**：直接操作真实 DOM 是非常昂贵的，而虚拟 DOM 可以在内存中快速进行比较和计算差异。Diff 算法帮助减少了更新操作的次数和范围，从而提升了页面渲染的性能。
②**批量更新**：Diff 算法能够<u>___将多次 DOM 更新操作合并为一次___</u>，避免了频繁的 DOM 操作，<u>___减少了浏览器的重排和重绘[^浏览器的重排和重绘]___</u>。
③**跨平台兼容**：虚拟 DOM 和 Diff 算法使得 Vue 可以运行在不同的平台上，例如浏览器、Weex 等，统一了渲染逻辑和数据响应式的实现。
④**更新效率**：即使是响应式系统可以自动更新视图，但是如果每次数据变化都直接操作真实 DOM可能会带来性能问题。Diff 算法可以智能地<u>___比较新旧 DOM 树的变化，只更新必要的部分___</u>，从而提高了更新效率。


#### 2.2 vue3 响应式设计原理
[前端面试题宝典-说说 vue3 中的响应式设计原理](https://fe.ecool.fun/topic/ea676360-c8f5-4ce4-bc66-5c3e4f7eddb6)
#### 2.3 Vue 的响应式数据流驱动页面和传统的事件绑定命令式驱动页面，分别有什么优缺点？
==TODO==
#### 2.4 vue 的响应式开发比命令式有哪些优势？
[前端面试题宝典-vue 的响应式开发比命令式有哪些优势？](https://fe.ecool.fun/topic/c578d161-5468-4577-90c2-98e46cb82604)
> ①**简化代码**：将数据和模板绑定起来实现视图更新的自动化，避免了手动操作 DOM 的繁琐和容易出错的操作。因此，可以大幅减少编写样板代码和调试代码所需的时间。
②**提高可维护性**：更方便地管理应用程序的状态，并对状态变化进行统一处理。这不仅可以提高代码的可读性和可维护性，还可以更方便地进行单元测试和集成测试。
③**增强用户体验**：可以实现局部更新、异步加载等功能，从而提升用户体验。例如，在列表中添加或删除项目时，只需要更新相应的项目，而不是重新渲染整个列表。又比如，在加载大量图片时，可以通过异步加载和懒加载的方式，提高页面加载速度和用户体验。
④**支持复杂组件设计**：支持组件化设计，它能够轻松地将一个大型应用程序拆分成多个小型、可重用的组件。这些组件可以根据需要进行嵌套和组合，形成更为复杂和丰富的 UI 界面，而且每个组件都具有独立的状态和生命周期。
#### 2.5 Proxy 和 Object.defineProperty
[前端面试题宝典-谈谈 Object.defineProperty 与 Proxy 的区别](https://fe.ecool.fun/topic/09420ad4-4cd0-4c3d-9b56-970e21c8f208)
##### 2.5.1 Vue 2 的响应式原理中 `Object.defineProperty` 有什么缺陷？
> ① **不能监听数组的变化**：在 Vue2.x 中解决数组监听的方法是将能够改变原数组的方法进行重写实现（比如：push、 pop、shift、unshift、splice、sort、reverse）
② **必须遍历对象的每个属性**：可以通过 Object.keys() 来实现
③ **必须深层遍历嵌套的对象**：通过递归深层遍历嵌套对象，然后通过 Object.keys() 来实现对每个属性的劫持

注：数组方法重写
```js
// 重写 push 方法
const originalPush = Array.prototype.push
Array.prototype.push = function() {
  // 进行数据劫持
  originalPush.apply(this, arguments)
}
```
##### 2.5.2 Vue3.0里为什么要用 `Proxy` API 替代 `Object.defineProperty` API ？
> ① **Proxy 针对整个对象**，Object.defineProperty 针对单个属性，这就解决了 需要对对象进行深度递归（支持嵌套的复杂对象劫持）实现对每个属性劫持的问题
② **Proxy可以劫持数组**，解决了 Object.defineProperty 无法劫持数组的问题
③ **有更多的拦截方法**，对比一些新的浏览器，可能会对 Proxy 针正对性的优化，有助于性能提升
```js
const aryProxy = new Proxy(ary, {
    get(target, property, receiver){
        return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
        return Reflect.set(target, property, value, receiver)
    }
})
```
#### 2.6 vue3 的响应式库是独立出来的，如果单独使用是什么样的效果？
==TODO==
### 3.虚拟DOM和diff算法
#### 3.1 Vue 是如何实现 MVVM 的？
==TODO==
#### 3.2 虚拟DOM及其实现
##### 3.2.1 什么是虚拟DOM？如何实现一个虚拟DOM？说说你的思路
[前端面试宝典-什么是虚拟DOM？如何实现一个虚拟DOM？说说你的思路](https://fe.ecool.fun/topic/d5acd6cf-38c3-4afb-965d-be79f03cd045)
==TOREAD==
##### 3.2.2 虚拟dom渲染到页面的时候，框架会做哪些处理？
[前端面试宝典-虚拟dom渲染到页面的时候，框架会做哪些处理？](https://fe.ecool.fun/topic/73f4551c-2b47-4172-8c34-1c08d9adf30f)
根据Diff算法的结果进行DOM的创建、更新和删除操作。这样可以最小化对真实DOM的改动，提高性能，并确保页面与新的虚拟DOM保持同步。处理事件绑定和触发生命周期钩子函数，以便提供更多的开发扩展能力和灵活性。
> ①**Diff算法**：框架会将新的虚拟DOM与旧的虚拟DOM进行对比，找出它们之间的差异。这个过程被称为Diff算法。Diff算法的目标是通过最小化操作次数来更新真实DOM，以提高性能。
②**创建和更新DOM节点**：根据Diff算法的结果，框架会创建或更新需要改变的DOM节点。如果一个节点在新的虚拟DOM中存在但在旧的虚拟DOM中不存在，框架会创建该节点并添加到页面上。如果一个节点在新的虚拟DOM和旧的虚拟DOM中都存在，但其属性或子节点发生变化，框架会更新相应的DOM节点。这样可以确保只有实际需要更改的部分才会重新渲染，减少不必要的操作。
③**处理事件绑定**：框架会重新绑定事件处理程序，以便在更新后正确响应用户交互。这包括添加、更新或删除事件监听器。
④**卸载节点**：如果一个节点在新的虚拟DOM中不存在但在旧的虚拟DOM中存在，框架会从页面上移除该节点。这可以防止内存泄漏和资源浪费。
⑤**触发生命周期钩子**：在渲染到页面后，框架会触发相应的生命周期钩子函数（如Vue中的mounted），以便开发人员可以在适当的时机执行自定义操作。

#### 3.3 说说vue中的diff算法
[前端面试宝典-说说vue中的diff算法](https://fe.ecool.fun/topic/7d27dc57-5d95-4e3f-88a7-eb685b7c21e4)
==TOREAD==
#### 3.4 vue中key的原理
[前端面试宝典-说说vue中，key的原理](https://fe.ecool.fun/topic/17f833d6-5312-4150-abb8-f3a320581109)
key 是用于帮助 Vue **识别和跟踪虚拟 DOM 的变化的特殊属性**。当 Vue 更新渲染真实 DOM 时，它使用 key 属性来比较新旧节点，以确定节点的更新、移动或删除，并尽可能地复用已存在的真实 DOM 节点，以提高性能。

key 必须是唯一且稳定的，最好使用具有唯一标识的值，例如使用数据的唯一 ID。同时，不推荐使用随机数作为 key，因为在每次更新时都会生成新的 key，导致所有节点都重新渲染，无法复用已有的节点，降低性能。

使用 key 可以提供更准确的节点识别和跟踪，避免出现一些常见的问题，比如在列表中重新排序时导致的元素闪烁、输入框内容丢失等。

虚拟 DOM 的 diff 算法通过 key 属性来判断两个节点是否代表相同的实体，而不仅仅是根据它们的内容是否相同。这样可以保留节点的状态和避免不必要的 DOM 操作。

>工作原理如下：
①当 Vue 更新渲染真实 DOM 时，它会对新旧节点进行比较，找出它们之间的差异。
②如果两个节点具有相同的 key 值，则 Vue 认为它们是相同的节点，会尝试复用已存在的真实 DOM 节点。
③如果节点具有不同的 key 值，Vue 会将其视为不同的节点，并进行适当的更新、移动或删除操作。

### 4.其他
#### 为什么Vue中的v-if和v-for不建议一起用?
#### Vue中的 v-show 和 v-if 有什么区别
#### Vue是怎么把template模版编译成render函数的？
[前端面试宝典-Vue是怎么把template模版编译成render函数的？](https://fe.ecool.fun/topic/926774f3-32a9-453c-84e4-0c873323b3ee)
==TOREAD==
#### Vue 模板是如何编译的？
[前端面试宝典-Vue 模板是如何编译的？](https://fe.ecool.fun/topic/418ef81f-96c6-4c4e-b218-df29be84890d)
#### Vue中的$nextTick有什么作用？
#### Vue2.0为什么不能检查数组的变化，该怎么解决？
#### Vue 中，假设 data 中有一个数组对象，修改数组元素时，是否会触发视图更新？
#### Vue中给对象添加新属性时，界面不刷新怎么办?
#### 说一下 vm.$set 原理
#### VNode属性
[前端面试宝典-VNode 有哪些属性？](https://fe.ecool.fun/topic/20be1725-4902-4135-8b80-7be60507fc13)
```js
export default class VNode {
  tag: string | void;
  data: VNodeData | void;
  children: ?Array<VNode>;
  text: string | void;
  elm: Node | void;
  ns: string | void;
  context: Component | void; // rendered in this component's scope
  functionalContext: Component | void; // only for functional component root nodes
  key: string | number | void;
  componentOptions: VNodeComponentOptions | void;
  componentInstance: Component | void; // component instance
  parent: VNode | void; // component placeholder node
  raw: boolean; // contains raw HTML? (server only)
  isStatic: boolean; // hoisted static node
  isRootInsert: boolean; // necessary for enter transition check
  isComment: boolean; // empty comment placeholder?
  isCloned: boolean; // is a cloned node?
  isOnce: boolean; // is a v-once node?

  constructor (
    tag?: string,
    data?: VNodeData,
    children?: ?Array<VNode>,
    text?: string,
    elm?: Node,
    context?: Component,
    componentOptions?: VNodeComponentOptions
  ) {
    /*当前节点的标签名*/
    this.tag = tag
    /*当前节点对应的对象，包含了具体的一些数据信息，是一个VNodeData类型，可以参考VNodeData类型中的数据信息*/
    this.data = data
    /*当前节点的子节点，是一个数组*/
    this.children = children
    /*当前节点的文本*/
    this.text = text
    /*当前虚拟节点对应的真实dom节点*/
    this.elm = elm
    /*当前节点的名字空间*/
    this.ns = undefined
    /*编译作用域*/
    this.context = context
    /*函数化组件作用域*/
    this.functionalContext = undefined
    /*节点的key属性，被当作节点的标志，用以优化*/
    this.key = data && data.key
    /*组件的option选项*/
    this.componentOptions = componentOptions
    /*当前节点对应的组件的实例*/
    this.componentInstance = undefined
    /*当前节点的父节点*/
    this.parent = undefined
    /*简而言之就是是否为原生HTML或只是普通文本，innerHTML的时候为true，textContent的时候为false*/
    this.raw = false
    /*静态节点标志*/
    this.isStatic = false
    /*是否作为跟节点插入*/
    this.isRootInsert = true
    /*是否为注释节点*/
    this.isComment = false
    /*是否为克隆节点*/
    this.isCloned = false
    /*是否有v-once指令*/
    this.isOnce = false
  }

  // DEPRECATED: alias for componentInstance for backwards compat.
  /* istanbul ignore next https://github.com/answershuto/learnVue*/
  get child (): Component | void {
    return this.componentInstance
  }
}
```
- __v_isVNode: true，内部属性，有该属性表示为Vnode
- __v_skip: true，内部属性，表示跳过响应式转换，reactive转换时会根据此属性进行判断
- isCompatRoot?: true，用于是否做了兼容处理的判断
- type: VNodeTypes，虚拟节点的类型
- props: (VNodeProps & ExtraProps) | null，虚拟节点的props
- key: string | number | null，虚拟阶段的key，可用于diff
- ref: VNodeNormalizedRef | null，虚拟阶段的引用
- scopeId: string | null，仅限于SFC(单文件组件)，在设置currentRenderingInstance当前渲染实例时，一期设置
- slotScopeIds: string[] | null，仅限于单文件组件，与单文件组件的插槽有关
- children: VNodeNormalizedChildren，子节点
- component: ComponentInternalInstance | null，组件实例
- dirs: DirectiveBinding[] | null，当前Vnode绑定的指令
- transition: TransitionHooks<HostElement> | null，TransitionHooks
- DOM相关属性
  - el: HostNode | null，宿主阶段
  - anchor: HostNode | null // fragment anchor
  - target: HostElement | null ，teleport target 传送的目标
  - targetAnchor: HostNode | null // teleport target anchor
  - staticCount: number，包含的静态节点的数量
  - suspense 悬挂有关的属性
  - suspense: SuspenseBoundary | null
  - ssContent: VNode | null
  - ssFallback: VNode | null
  - optimization only 用于优化的属性
  - shapeFlag: number
  - patchFlag: number
  - dynamicProps: string[] | null
  - dynamicChildren: VNode[] | null
根节点会有的属性
- appContext: AppContext | null，实例上下文


## 四、组件、插件、指令、混入
#### vue的祖孙组件的通信方案有哪些？
#### Vue组件间通信方式都有哪些? 
#### Vue中组件和插件有什么区别？
#### vue 是如何识别和解析指令的？
==TODO==
#### 说说你对vue的mixin的理解，以及有哪些应用场景？
#### Vue怎么实现权限管理？控制到按钮级别的权限怎么做？
#### 自定义指令是什么？有哪些应用场景？
#### 大型项目中，Vue项目怎么划分结构和划分组件比较合理呢？
#### 说说你对slot的理解？slot使用场景有哪些？

## 五、优化
#### Vue 项目中，你做过哪些性能优化？
#### 单页应用如何提高加载速度？
#### SPA（单页应用）首屏加载速度慢怎么解决？
#### Vue3.0 性能提升主要是通过哪几方面体现的？
#### Vue3.0的设计目标是什么？做了哪些优化?

## 六、缓存
#### Vuex 是什么？
#### 说说 Vuex 的原理
#### vuex中的辅助函数怎么使用？
#### Vuex有几种属性，它们存在的意义分别是什么？
#### 刷新浏览器后，Vuex的数据是否存在？如何解决？

## 七、路由
#### vue 中 $route 和 $router 有什么区别？
#### vue路由中，history和hash两种模式有什么区别？
#### 说说你对Vue中 keep-alive 的理解

## 八、框架、对比
#### 说说你对vue的理解?
#### 说说你对渐进式框架的理解
#### react 和 vue 有什么区别？
#### React 和 Vue 在技术层面有哪些区别？
#### 为什么 react 需要 fiber 架构，而 Vue 却不需要？
#### vue-cli 有哪些功能？
==TODO==
#### 说下Vite的原理
#### vue-loader做了哪些事情？
![](https://img2024.cnblogs.com/blog/3435172/202410/3435172-20241015133422376-549837048.png)

#### SSR[^CSR、SSR、SSG、NSR、ESR、ISR]是什么？Vue中怎么实现？

## 九、业务应用：部署、跨域、调试
#### 怎么处理vue项目中的错误的？
#### Vue项目如何进行部署？是否有遇到部署服务器后刷新404问题？
#### Vue项目中如何解决跨域问题？
#### Vue项目中有封装过axios吗？怎么封装的？

[^浏览器的重排和重绘]:[怎么理解回流跟重绘？什么场景下会触发](https://fe.ecool.fun/topic/417ebda0-3f2d-48d3-95ec-ae1838bf39cb)
[^CSR、SSR、SSG、NSR、ESR、ISR]:CSR、SSR、SSG、NSR、ESR、ISR 都是什么？==TODO== 
 [CSR和SSR分别是什么？](https://fe.ecool.fun/topic/c50852c5-6471-4bd3-8392-02ab58e4c726)