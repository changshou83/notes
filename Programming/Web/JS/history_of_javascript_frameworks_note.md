**[History of Javascript Frameworks](https://programmingsoup.com/history-of-javascript-frameworks)的简单记录**

## 框架的诞生

<details>
<summary><strong>JS的转折点</strong></summary>
<main>
<h2>第一个是Google的AJAX技术。</h2>

为什么？AJAX出现之前JS没什么太大作用，给网页加加特效可能有用，而且还有浏览器大战遗留的兼容性问题。

AJAX的作用？使用JS对网页进行局部刷新，而不用重新加载页面，提供了更好的用户体验以及制作WebAPP的可能性。

<h2>第二个是ES6。</h2>

为什么？ES6之前JS只是一门简单的脚本语言。ES6带来了新的特性和重要的模块化支持，为之后的蓬勃发展带来基础。

列举常用新特性：新的标准库，解构赋值，箭头函数，Rest和Spread语法，模板字符串，类，新的变量定义关键字以及重要的模块化支持。
</main>
</details>


**AJAX**让开发人员有了新的想法，但是这也**增加了项目的复杂度，并且兼容性问题仍然存在**。初期的框架正是为了解决这些问题而出现的。

常见的框架：
1. Prototype.js
2. Dojo
3. Mootools
4. YUI
5. JQuery

他们旨在解决以下问题：
1. 简化AJAX和动态加载
2. 操作DOM
3. 模块化
4. 浏览器兼容性
5. 动画

可是这些框架解决了问题，但是没有完全解决，这些方案不能支持更大的复杂度和更好的可维护性，例如容易写出大片的面条代码。

为了支持更大项目的复杂度和可维护性，MV*框架出现。

## MV*框架的兴起

### 什么是MV*框架

就是MVC及其变种。

MVC由三部分组成：
- View：视图层，控制程序的展示
- Controller：控制层，内含程序的逻辑
- Model：模型层，管理程序的数据

三者间的关系：模型更新视图，视图通过用户行为触发控制器，控制器读写模型。

之后又出现了新的MVC变种：MVP和MVVM

P是指Presenter，即呈现层。P与C的不同在于，前者包含了C中的业务逻辑部分和M中负责更新视图的部分。M通过事件通知P去更新V，由此看出，P将M和V完全隔离开来。

VM是指View Model，即视图模型层。它是基于P以上，简化了V与P之间的关系，将视图与VM中的数据进行双向绑定，以进行数据和视图的自动更新与同步。除此之外，还将P中的业务逻辑挪到了模型层中。

### MV*框架的历史

从2010年开始，JS框架进入MV*时代。

MVP的代表人物：Backbone.js(库)
MVVM的代表人物：Knockout.js(库)，AngularJS(框架)

Knockout.js的示例程序：
```html
<script>
  const vm = { price: ko.observable(10), increase: () => this.price++ }
</script>
<p>The price is <span data-bind="text:price"></span></p>
<button data-bind="click:increase">Increase the price</button>
```

Backbone.js的示例程序：
```html
<div id="price-container"></div>

<!-- View -->
<script type="text/template" id="price-template">
  <p>The price is <%- price %></p>
  <button id="increase-btn">Increase the price</button>
</script>

<script>
  /** Model **/
  const PriceModel = Backbone.Model.extend({
    default: { price: 10 }
  })

  /** Presenter **/
  const PriceView = Backbone.View.extend({
    el: '#price-container',
    template: _.template($('#price-template'.html())),
    events: { 'click #increase-btn': 'increase' },
    initialize() {
      // 视图改变时调用render
      this.listenTo(this.model, 'change', this.render);
      // render on create
      this.render();
    },
    render() {
      const valuesForTemplate = this.model.toJSON();
      this.$el.html(this.template(valuesForTemplate));
      return this;
    },
    increase() {
      this.model.set('price', this.model.get('price') + 1)
    }
  })

  /** Create the View **/
  new PriceView({ model: new PriceModel() })
</script>
```

更多：
1. Ember.js：一个基于约定大于配置的MVVM框架
2. Meteor.js：一个平台类的同构框架

`MV*`框架提高了开发效率，但是开发者对DX提出了要求，而`MV*`对结构的要求对DX并不友好，因此进入了三大框架时代。

## 三大框架时代

三大框架分别是：React(库)，Vue(框架)和Angular(框架)

其中React和Vue关注视图，路由状态管理和API交互则由社区解决，Angular是一个成熟的框架，其内置了许多现成的工具。

### React

React相当于MVC中的V，其使用JSX编写视图。它的特点是数据是单向流动的。

早期，其依赖类组件，React16以后，函数组件成为主流。

React类组件：
```jsx
export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      price: 10
    };
    this.increase = this.increase.bind(this);
  }
  increase() {
    this.setState((prev) => ({
      price: prev.price + 1
    }))
  }
  render() {
    return (
      <div>
        <p>The price is {this.state.price}</p>
        <button onClick={this.increase}>Increase the price</button>
      </div>
    )
  }
}
```

React函数组件：
```jsx
export default function App() {
  const [price, setPrice] = useState(10)

  return (
    <div>
      <p>The price is {price}</p>
      <button onClick={() => setPrice(price + 1)}>Increase the price</button>
    </div>
  )
}
```

### Vue

Vue是MVVM中的VM，其使用SFC来表达页面。

Vue2的SFC：
```vue
<template>
  <p>The price is {price}</p>
  <button @click="increase">Increase the price</button>
</template>

<script>
export default {
  data: {
    price: 10
  },
  methods: {
    increase() {
      this.price += 1;
    }
  }
}
</script>
```

Vue3的SFC：
```vue
<script setup>
import { ref } from 'vue';

const price = ref(10);
const increase = () => price.value += 1;
</script>

<template>
  <p>The price is {price}</p>
  <button @click="increase">Increase the price</button>
</template>
```

### Angular

Angular 与 AngularJS 不同。

## 数据绑定

数据绑定是在数据与UI之间创建连接的操作。

看看在各大MVVM库和框架中是如何使用数据绑定的。
- `Knockout.js`
  - 创建：`ko.observable(value)`(双向绑定)，直接在`ko.applyBindings()`里声明的是响应式变量
  - 获取：`variable()`，在HTML元素上添加`data-bind`属性，值为`name1: variable1, name2: variable2`
  - 修改：`variable(newValue)`
  - 激活`knockout`：`ko.applyBindings({ variable: ko.observable(value) })`
- `Vue`：
  - 创建：在data函数中声明(Vue2)，`variable = ref(value), variable = reactive({ name: value })`(Vue3)
  - 使用：`this.variable`(Vue2)，`variable.value`(Vue3)，在模板中使用v-bind命令绑定响应式变量，使用v-model绑定的是双向绑定
  - 修改：对使用变量直接赋值
- `Angular`：使用`@Component`装饰器，组件类中的变量都是响应式变量，模板中使用执行绑定指定响应式变量即可

## 编译型框架的兴起

- Svelte：Zero Virtual DOM，纯编译型框架，DSL，社区更繁荣
- Solid：Zero Virtual DOM，纯编译型框架，JSX，社区待发展

## SSR框架的兴起

CSR对SEO不友好，所以出现一堆SSR和SSG框架。
SSR提升了SEO和首屏渲染，但是增加了服务器成本。

常见的Nextjs(React)，Nuxt(Vue)，SvelteKit(React)，Gatsby(React)，Astro，Qwik。

未来将是两者结合。

### 注水操作

为SSR出来的HTML添加交互性。

方法：
1. 将监听器附加到DOM节点
2. 构建框架内部组件的状态

在SSR初次内容渲染出来与注水操作完成之间的时间，整个页面虽然看起来完整了，但是不具备交互性，也就是虽然首屏渲染提前了，但是TTI没有对应的优化。

### 解决水合问题

主要思路是：将我们的APP分解成块，让我们可以根据需要更智能的进行水合。

现在的四种实现：
1. 岛屿架构：本质是使用部分水合将应用程序划分为交互性孤岛。
2. 部分水合：通过服务器分析哪些部分需要水合，不需要水合的组件只发送HTML，需要水合的组件则发送一个交互式UI组件。
3. 渐进式水合：根据事件/交互逐步加载代码并水合页面，问题是APP的大部分在初始阶段就会被加载，因此无论如何都必须加载所有JS，因此不是很好的方案。
4. RSC：在所有HTML和JS加载完毕前开始水合，优先考虑对需要交互的组件进行水合。

实现岛屿架构的框架：
1. Astro：SSG，框架无关
2. Fresh：SSR，Deno，JSX，Preact

可恢复性框架：Qwik
主打一个极限的需要的代码才加载，其他SSR至少要把JS全量加载，这个连JS都是部分加载的。
加载什么代码由QwikLoader决定，使用WebWorker实现。

## 你需要一个框架吗

