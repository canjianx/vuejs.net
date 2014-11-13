title: 全局 API
type: api
order: 5
---

### Vue.config

`Vue.config`是一个包含了Vue的全局设置的一个对象。这里是一个有效的设置项列表和它们的默认值：

``` js
{
  // 是否打印堆栈信息？
  debug: true,
  // 指示器前缀
  prefix: 'v-',
  // 插入到HTML的插入分隔符， add
  // 1 extra outer-most character.
  delimiters: ['{&#123;', '&#125;}'],
  // 不要警告信息？
  silent: false,
  // 插入大括号绑定？
  interpolate: true,
  // 使用异步更新(for directives & watchers)？
  async: true,
  // 允许修改监控数组的原型链？
  proto: true
}
```

你可以直接改这些设置项。例如：

``` js
Vue.config.debug = true // turn on debugging mode
```

修改插入符的例子：

``` js
Vue.config.delimiters = ['(%', '%)']
// tags now are (% %) for text
// and ((% %)) for HTML
```

### Vue.extend( options )

- **options** `Object`

基于Vue构造函数创建一个`子类`。所有[实例化选项](/api/options.html)都可以在这里使用。这里需要注意的是`el`和`data`，这里必须是函数。

在内部，`Vue.extend()`在实例化组件之前在所有的实例选项中调用。关于组件更多的信息请看[组件系统](/guide/components.html)。

**例子**

``` js
var Profile = Vue.extend({
  el: function () {
    return document.createElement('p')
  },
  template: '&#123;&#123;firstName&#125;&#125; &#123;&#123;lastName&#125;&#125; aka &#123;&#123;alias&#125;&#125;'
})
var profile = new Profile({
  data: {
    firstName : 'Walter',
    lastName  : 'White',
    alias     : 'Heisenberg'
  }  
})
profile.$appendTo('body')
```

结果:

``` html
<p>Walter White aka Heisenberg</p>
```

### Vue.directive( id, [definition] )

- **id** `String`
- **definition** `Function` or `Object` *optional*

注册或者获取一个自定义的指示器。更多的信息请看[写一个自定义的指示器](/guide/custom-directive.html)。

### Vue.filter( id, [definition] )

- **id** `String`
- **definition** `Function` *optional*

注册或者获取一个自定义的过滤器。更多的信息请看[写一个自定义的过滤器](/guide/custom-filter.html)。

### Vue.component( id, [definition] )

- **id** `String`
- **definition** `Function Constructor` or `Object` *optional*

Register or retrieve a global component. For more details see [Component System](/guide/components.html).

注册或者获取一个全局的组件。更多的信息请看[组建系统](/guide/components.html)。

### Vue.transition( id, [definition] )

- **id** `String`
- **definition** `Object` *optional*

注册或者获取一个全局的Javascript转化效果。更多的信息请看[javascript转化](/guide/transitions.html#JavaScript_Functions)。


### Vue.partial( id, [definition] )

- **id** `String`
- **definition** `String | Node` *optional*

注册或者获取一个全局的片段。它可以是一个模板文本，`#`开头的选择器，一个DOM元素，或者一个文档片段。

**例子**

HTML

``` html
<div id="demo">
  &#123;&#123;> avatar&#125;&#125;
</div>
```

JavaScript

``` js
Vue.partial('avatar', '&lt;img v-attr="src:avatarURL"&gt;')

new Vue({
  el: '#demo',
  data: {
    avatarURL: '/images/avatar.jpg'
  }    
})
```

结果:

``` html
<div id="demo">
  <img src="/images/avatar.jpg">
</div>
```

### Vue.nextTick( callback )

- **callback** `Function`

Vue.js批量进行视图更新而且全部异步执行它们。如果有效并且`setTimeout(fn, 0)`的时间到了，它会使用`requestAnimationFrame`。这个方法再下一个视图更新后调用回调函数，这个在你想等到视图更新是有效的。

### Vue.use( plugin, [args...] )

- **plugin** `Object` or `Function`
- **args...** *optional*

增加一个Vue.js的插件。如果插件是一个对象，它必须有一个`install`方法。如果他自己就是一个函数，它将会被当成`install`方法。这个方法将会把Vue当成参数调用。更多信息请看[插件](/guide/extending.html#Extend_with_Plugins)。

### Vue.util

暴露内部的`util`模块，它包含了很多实用的方法。这些给高级的插件/指示器准备的，所以你需要看看源码他们是怎样的。