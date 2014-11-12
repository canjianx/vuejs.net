title: 构建大型程序
type: guide
order: 13
---

Vue.js被设计的尽可能的灵活 - 它只是个接口库，并不能增强任何架构决定。做一个快速的原型它是非常有用，那些缺乏通过它构建大型的可伸缩的程序经验的人来说是个挑战。下面是一个个人的观点，关于怎样使用Vue.js组织一个大型的工程。

## 模块化

虽然独立编译的vue.js可以被当成全局变量来使用，但利用模块化构建系统来组织你的代码通常更好。做这件事推荐的方式是用CommonJS模块来写你的代码(在Node.js中使用的格式，vue.js中也用了这种方式)，然后使用[Browserify](http://browserify.org/)或者[Webpack](http://webpack.github.io/)打包

这里是在Github上的一些构建的例子：

- [Vue + Browserify](https://github.com/vuejs/vue-browserify-example)
- [Vue + Webpack](https://github.com/vuejs/vue-webpack-example)

## 单文件组件

在典型的vue.js工程中，我们把我们的代码分解到很多小的组件中，每个组件分装自己的css样式是很好的，模板和javascript定义在同一个地方。使用前面提到的构建工具的好处是他们都提供把代码合并到一起的机制，使用预处理我们可以像这样写我们的组件：

<img src="/images/vueify.png">

如果你有预编译器，你可以这样：

<img src="/images/vueify_with_pre.png">

这是为Browserify使用[Vueify](https://github.com/vuejs/vueify)达到的或者为Webpack通过[Vue-loader](https://github.com/vuejs/vue-loader)达到的。

## 路由

You can implement some rudimentary routing logic by manually listening on hashchange and utilizing a dynamic `v-component`.

你可以通过手动的监听哈希修改和使用动态`v-component`实现一些基本的路由逻辑。

**例子:**

``` html
<div id="app">
  <div v-component="{&#123;currentView&#125;}"></div>
</div>
```

``` js
Vue.component('home', { /* ... */ })
Vue.component('page1', { /* ... */ })
var app = new Vue({
  el: '#app',
  data: {
    currentView: 'home'
  }
})
// Switching pages in your route handler:
app.currentView = 'page1'
```

使用这个机制使用独立的路由库如[Page.js](https://github.com/visionmedia/page.js)或者[Director](https://github.com/flatiron/director)是很容易的。

## 和服务器沟通


所有的Vue实例都可以直接使用`JSON.stringify()`串行化它们自己的`$data`。你可以使用任何你喜欢的ajax组件，例如[SuperAgent](https://github.com/visionmedia/superagent)。它也可以和无后端服务例如Firebase很好的配合使用。

## 单元测试

兼容CommJS的构建系统的所有事情都是可以工作的。有一个推荐是使用[Karam](http://karma-runner.github.io/0.12/index.html)和[CommonJS pre-processor](https://github.com/karma-runner/karma-commonjs)来进行测试。

The best practice is to export raw options / functions inside modules. Consider this example:

最佳实践是导出模型里面的未处理的选项和函数。考虑这个例子：

``` js
// my-component.js
module.exports = {
  created: function () {
    this.message = 'hello!'
  }
}
```

你可以在你整个模块中使用这个文件：

``` js
// main.js
var Vue = require('vue')
var app = new Vue({
  el: '#app',
  data: { /* ... */ },
  components: {
    'my-component': require('./my-component')
  }
})
```

你可以像这样测试这个模块：

``` js
// Some Jasmine 2.0 tests
describe('my-component', function () {  
  // require source module
  var myComponent = require('../src/my-component')
  it('should have a created hook', function () {
    expect(typeof myComponent.created).toBe('function')
  })
  it('should set correct default data', function () {
    expect(typeof myComponent.data).toBe('function')
    var defaultData = myComponent.data()
    expect(defaultData.message).toBe('hello!')
  })
})
```

<p class="tip">因为Vue.js绑定的更新是异步的，当判断数据修改之后Dom的更新你应该使用`Vue.nextTick()`</p>

## 一个例子

[Vue.js Hackernews Clone](https://github.com/yyx990803/vue-hackernews)是一个例子程序，它使用了Browserify + Vueify来组织代码，使用Director.js路由，还有HackerNews的官方Firebase API作为后端。它绝不是一个大的应用程序，但是上面讨论的这些概念它都使用了。