title: 开始
type: guide
order: 2
---

## 介绍

Vue.js是一个为了构建交互性web接口的库。

技术上，Vue.js关注点在MVVM模式的[ViewModel](#ViewModel)层。它通过双向数据绑定来连接[View](#View)和[Model](#Model)。实际的Dom操作和输出格式化被抽象到[Directives]()和[Filters](#Filters)。

在哲学上，目标是提供通过尽可能简单的API来实现的双向数据绑定和可组合的视图组件的好处。它不是一个完整的框架 - 它被设计成一个简单的灵活的视图层。你可以单独它来快速进行原型设计，也可以和其他库结合使用来自定义前端。它也是无后端服务的例如`Firebase`的天作之合。

Vue.js的API深受[AngularJS]，[KnockoutJS]，[Ractive.js]以及[Rivets.js]的影响。尽管类似，但是我相信Vue.js给那些已存在的库在简单和功能之间寻找平衡提供了可选的价值。

即使你已经很熟悉他们中的一些，这里推荐你去阅读概览因为你对这些的见解可能和他们在Vue.js环境下不太一样。

## 概览

### ViewModel

一个同步Model和View的对象。在Vue.js，每一个Vue实例是一个ViewModel。他们是`Vue`构造函数或者他们子类的实例化：

```js
var vm = new Vue({ /* options */ })
```

做为一个使用Vue.js的开发者，这是你碰到的首要的对象。更多内容请看[Class: Vue](/api/)

### View

被Vue对象管理的实际的DOM。

```js
vm.$el // The View
```

Vue.js使用基于DOM的模板，每一个Vue实例和一个对应的DOM元素关联。当一个Vue实例被创建，它递归的遍历根节点下所有的子节点同时设置必要的数据绑定。View完成编译，它就和数据之间完成了双向绑定。

当使用Vue.js，你几乎不需要自己接触DOM，除非你要自定义directives（稍后解释）。当数据变了的时候视图的更新将会自动被触发。视图的更新是高度颗粒化到一个文本节点。为了更好的效率它们还是批量和异步执行的。

### Model

A slightly modified plain JavaScript object.

一个稍稍改动的普通的Javascript对象。

```js
vm.$data // The Model
```

在Vue.js，模型是简单的javascript对象，或者**data objects**。你可以管理它们的属性而且变换可以被观察它们的Vue实例感知到。这里的Vue.js实现是通过转换data的属性到ES5的getting/settings上。这里不需要脏检查，也不需要精确的通知Vue去更新视图。无论何时数据更新，视图都会在下一帧的时候更新。

Vue实例代理了所有它观察的对象的属性。所以一旦一个对象`{a: 1}`被观察，`vm.$data.a`和`vm.a`将会返回相同的值，而且`vm.a = 2`将会修改`vm.$data`。

数据对象在某处被修改，修改`vm.$data`有一样的效果。在大型的软件中对待Vue对象为纯的视图，而且数据管理逻辑外形化到更离散的存储层是被推荐的。

这里有一个需要警告的是一旦这种观察的关系初始化，Vue.js将不会观察到新的增加和删除属性操作。要应付这个问题，需要被观察的对象增强`$add`和`$delete`方法。

### Directives

HTML前置属性，告诉Vue.js对Dom元素做的事情。

```html
<div v-text="message"></div>
```

这里是一个div元素，有一个带有值为`message`的`v-text`指令。它所做的事是告诉Vue.js这个div的文本内容需要和Vue实例中的`message`属性保持同步。

指令可以减少随意的Dom操作。例如`v-attr`操作元素属性，`v-repeat`基于数组克隆元素，`v-on`绑定事件监听。。。我们将在稍后解释它。

### Mustache Bindings

你也可以使用mustache风格的绑定，在文本中和在属性中都可以。他们将会在钩子下转成`v-text`和`v-attr`指令。例如：

```html
<div id="person-&#123;&#123;id&#125;&#125;">Hello &#123;&#123;name&#125;&#125;!</div>
```

虽然它比较便利，这里有一些事情需要知道：

<p class="tip">`<image>`元素的`src`属性会在值被设置的时候会生成一个http请求，所以当模板第一次被解析的时候见会产生一个404。这种情况下优先使用`v-attr`</p>

<p class="tip">当解析HTML的时候IE 将会删除没作用的内部`style`属性，所以当你绑定内嵌的CSS的时候如果你想支持IE总是使用`v-style`</p>

你可以使用三个大括号来表示非转义的HTML，实际上它转换成`v-html`了。

``` html
{&#123;&#123; safeHTMLString &#125;&#125;}
```

然而，这样可能导致潜在的XSS攻击，因此建议只有在你特别肯定数据源的安全的时候使用三个大括号，或者让它通过一个自定义的可以清楚不安全的html。

最后，你可以增加`*`到你的大括号绑定来指示这是一个一次性的绑定，这种绑定对数据变化不做相应。

``` html
{&#123;* onlyOnce &#125;}
```

### 过滤器

过滤器是更新View之前处理原始数据的函数，它们可以定义在指令和绑定内部：

```html
<div>&#123;&#123;message | capitalize&#125;&#125;</div>
```

现在，在div的文字内容被更新之前，`message`的值将会首先通过`capitalize`函数，更多内容看[深入过滤器](/guide/filters.html).

### 组件

In Vue.js, every component is simply a Vue instance. Components form a nested tree-like hierarchy that represents your application interface. They can be instantiated by a custom constructor returned from `Vue.extend`, but a more declarative approach is registering them with `Vue.component(id, constructor)`. Once registered, they can be declaratively nested in other Vue instance's templates with the `v-component` directive:

在vue.js，每个组件都是一个简单的Vue实例。组件形成的嵌套的树状继承结结构正好显示了你的程序接口。他们可以被从`Vue.extend`返回的自定义的构造函数实例化，但是更有说明性的方法是使用`Vue.component(id, constructoor`来注册他们，他们可以通过`v-compoent`指示器嵌套到别的Vue实例模板中。

``` html
<div v-component="my-component">
  <!-- internals handled by my-component -->
</div>
```

这种简单的机制可以说性的重用，而且可以类似[Web 组件](http://www.w3.org/TR/components-intro/)来构造Vue实例，不需要最新的浏览器或者重型的填充。通过把一个程序分割层一些小的组件，结果是一个高度解耦和可管理的代码基。更多细节看[组件系统](/guide/components.html)

## 快速例子

``` html
<div id="demo">
  <h1>&#123;&#123;title | uppercase&#125;&#125;</h1>
  <ul>
    <li
      v-repeat="todos"
      v-on="click: done = !done"
      class="&#123;&#123;done ? 'done' : ''&#125;&#125;">
      &#123;&#123;content&#125;&#125;
    </li>
  </ul>
</div>
```

``` js
var demo = new Vue({
  el: '#demo',
  data: {
    title: 'todos',
    todos: [
      {
        done: true,
        content: 'Learn JavaScript'
      },
      {
        done: false,
        content: 'Learn Vue.js'
      }
    ]
  }
})
```

**结果**

<div id="demo"><h1>&#123;&#123;title | uppercase&#125;&#125;</h1><ul><li v-repeat="todos" v-on="click: done = !done" class="&#123;&#123;done ? 'done' : ''&#125;&#125;">&#123;&#123;content&#125;&#125;</li></ul></div>
<script>
var demo = new Vue({
  el: '#demo',
  data: {
    title: 'todos',
    todos: [
      {
        done: true,
        content: 'Learn JavaScript'
      },
      {
        done: false,
        content: 'Learn Vue.js'
      }
    ]
  }
})
</script>

也可以在[jsfiddle](http://jsfiddle.net/yyx990803/yMv7y/)看。

你可以点击一个Todo去触发，你也可以打开你的浏览器控制台，通过`demo`对象来玩一玩，比如修改`demo.title`，往`demo.todos`增加一个新的对象，或者触发一个todo的`done`状态。

你可能在脑海中有一些问题了，不要担心，我们将会很快解释它。下一节[深入指示器](/guide/directives.html)

[AngularJS]: http://angularjs.org
[KnockoutJS]: http://knockoutjs.com
[Ractive.js]: http://ractivejs.org
[Rivets.js]: http://www.rivetsjs.com