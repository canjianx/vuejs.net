title: 自定义指示器
type: guide
order: 9
---

## 基础

Vue.js允许你注册自定义的指示器，实际上能让你接触到Vue是怎样把数据改变映射到DOM行为上。你可以用`Vue.directive(id, definition)`方法注册一个全局的自定义指示器，传入指示器id，和指示器对象。在指示器对象中可以提供几个钩子函数(全部的选项)：

- **bind**: 在指示器第一次绑定的时候调用一次。
- **update**: 当绑定的值改变的时候调用，新值作为参数提供。
- **unbind**: 在指示器接触绑定的时候调用一次。

**例子**

``` js
Vue.directive('my-directive', {
  bind: function (value) {
    // do preparation work
    // e.g. add event listeners or expensive stuff
    // that needs to be run only once
    // `value` is the initial value
  },
  update: function (value) {
    // do something based on the updated value
    // this will also be called for the initial value
  },
  unbind: function () {
    // do clean up work
    // e.g. remove event listeners added in bind()
  }
})
```

一旦注册，你可以在Vue.js模板中像这样(你需要增加Vue.js的前缀)使用它：

``` html
<div v-my-directive="someValue"></div>
```

当你只需要`update`函数时，你只需要传递一个单独的函数，而非一个对象：

``` js
Vue.directive('my-directive', function (value) {
  // this function will be used as update()
})
```

所有的钩子函数都会被拷贝到实际的**指示器对象**中，在这些函数中你可以使用`this`来访问上下文。指示器对象暴露了一些有用的属性：

- **el**: 和指示器绑定的元素。
- **vm**: 指示器的上下文环境。
- **expression**: 绑定的表达式，不包含过滤器和参数。
- **arg**: 参数，如果存在的话。
- **raw**: 未解析的表达式。
- **name**: 不带前缀的指示器的名字。

<p class="tip">你应该把所有的指示器当成只读的而且避免修改它们。你可以给指示器对象增加自定义的属性，但是小心，不要覆盖已经存在的属性</p>

一个使用了一些属性的自定义指示器的例子：

``` html
<div id="demo" v-demo="LightSlateGray : msg"></div>
```

``` js
Vue.directive('demo', {
  bind: function () {
    this.el.style.color = '#fff'
    this.el.style.backgroundColor = this.arg
  },
  update: function (value) {
    this.el.innerHTML =
      'name - '       + this.name + '<br>' +
      'raw - '        + this.raw + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - '   + this.arg + '<br>' +
      'value - '      + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
```

**结果**

<div id="demo" v-demo="LightSlateGray : msg"></div>
<script>
Vue.directive('demo', {
  bind: function () {
    this.el.style.color = '#fff'
    this.el.style.backgroundColor = this.arg
  },
  update: function (value) {
    this.el.innerHTML =
      'name - ' + this.name + '<br>' +
      'raw - ' + this.raw + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - ' + this.arg + '<br>' +
      'value - ' + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
</script>

## 字面量指示器

当创建自定义指示器的时候你可以传递一个`isLiteral: true`，属性值将会被当成字符串字面量作为指示器的表达式。这个指示器将不会尝试去安装数据监控。

例子:

``` html
<div v-literal-dir="foo"></div>
```

``` js
Vue.directive('literal-dir', {
  isLiteral: true,
  bind: function () {
    console.log(this.expression) // 'foo'
  }
})
```

### 动态字面量

然而，当字面量中包含大括号，行为如下：

- 指示器实例将有一个标记`this._isDynamicLiteral` 设置成 `true`;

- 如果没有`update`函数，大括号里面的表达式将会被评估一次，并赋值给`this.expression`。没有数据监控发生。

- 如果有一个`update`函数，指示器**将会**设置数据监控，并且当数据发生变化是调用update.

## 双向指示器

如果你的指示器期望回写数据到Vue实例，你需要传递`twoWay: true`。这个属性允许在指示器内部使用`this.set(value)`。

``` js
Vue.directive('example', {
  twoWay: true,
  bind: function () {
    this.handler = function () {
      // set data back to the vm.
      // If the directive is bound as v-example="a.b.c",
      // this will attempt to set `vm.a.b.c` with the
      // given value.
      this.set(this.el.value)
    }.bind(this)
    this.el.addEventListener('input', this.handler)
  },
  unbind: function () {
    this.el.removeEventListener('input', this.handler)
  }
})
```

## 内部表达式

传递`acceptStatement: true`可以是你的自定义指示器像`v-on`一样接受内部表达式：

``` html
<div v-my-directive="a++"></div>
```

``` js
Vue.directive('my-directive', {
  acceptStatement: true,
  update: function (fn) {
    // the passed in value is a function which when called,
    // will execute the "a++" statement in the owner vm's
    // scope.
  }
})
```

明确的使用这个，因为一般情况下你想要在你的模板中避免单边影响。

## 指示器优先级

你可以选择性的为你的指示器提供一个优先级数字（默认是0）。在同一个元素上一个有高一点的优先级将会被处理的更早。相同优先级的会按照他们出现的顺序处理，但是这个顺序在不同的浏览器中不保证一样。

你可以在[API文档](/api/directives.html)中查看一些内部的指示器优先级。另外，`v-repeat`，`v-if`和`v-component`被视为"终结者指示器"在编译过程中他们拥有最高的优先级

接下来，我们将看看怎样[写一个过滤器](/guide/custom-filter.html)。