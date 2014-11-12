title: 扩展 Vue
type: guide
order: 14
---

## 使用Mixins扩展

Mixins是一个为Vue组件提供可用功能的灵活的方式。你可以像一个普通的vue组件选项对象写一个混入。

``` js
// mixin.js
exports.mixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}
```

``` js
// test.js
var myMixin = require('./mixin')
var Component = Vue.extend({
  mixins: [myMixin]
})
var component = new Component() // -> "hello from mixin!"
```

## 使用Plugins扩展

Plugins通常增加全局级别的功能。

### 写一个插件

这里有几类典型的插件你可以写：

1. 增加一个或多个全局方法。例如[vue-element](https://github.com/vuejs/vue-element)。
2. 增加一个或多个全局的内容：指示器/过滤器/转换。例如[vue-touch](https://github.com/vuejs/vue-touch)
3. 增加一些Vue实例方法通过把这些实例方法放到Vue.prototype。实例方法的惯例是加一个`$`前缀，这样你就不会合用户的数据和方法混淆。

``` js
exports.install = function (Vue, options) {
  Vue.myGlobalMethod = ...          // 1
  Vue.directive('my-directive', {}) // 2
  Vue.prototype.$myMethod = ...     // 3
}
```

### 使用一个插件

假设使用一个CommonJS的编译系统：

``` js
var vueTouch = require('vue-touch')
// use the plugin globally
Vue.use(vueTouch)
```

你可以传递额外的选项到插件：

```js
Vue.use('my-plugin', {
  /* pass in additional options */
})
```

## 已存在的工具

- [vue-devtools](https://github.com/vuejs/vue-devtools): 一个Chrome的开发工具为了调试vue程序。
- [vue-touch](https://github.com/vuejs/vue-touch): 使用Hammer.js实现的触摸手势指示器。
- [vue-element](https://github.com/vuejs/vue-element): 用vue.js注册之定义的元素。
- [用户贡献的工具列表](https://github.com/yyx990803/vue/wiki/User-Contributed-Components-&-Tools)