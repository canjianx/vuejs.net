title: 监听消息
type: guide
order: 6
---

你可以使用`v-on`指示器来绑定DOM事件的监听。它可以绑定到一个事件处理函数上，（不带调用括号的）或者内嵌表达式。如果提供了一个处理函数，它将会得到原始的DOM事件作为参数。这个事件对象增加了一个特殊的属性`targetVM`，指向触发这个时间的VM对象。

``` html
<div id="demo">
  <a v-on="click: onClick">Trigger a handler</a>
  <a v-on="click: n++">Trigger an expression</a>
</div>
```

``` js
new Vue({
  el: '#demo',
  data: {
    n: 0
  },
  methods: {
    onClick: function (e) {
      console.log(e.target.tagName) // "A"
      console.log(e.targetVM === this) // true
    }
  }
})
```

## 调用表达式处理

`targetVM`在`v-on`和`v-repeat`使用时是有用的，因为后者创建了很多子VM对象。然而，常常使用括号参数`this`更便捷，它相当于当前VM的上下文作用域。

``` html
<ul id="list">
  <li v-repeat="items" v-on="click: toggle(this)">{{text}}</li>
</ul>
```

``` js
new Vue({
  el: '#list',
  data: {
    items: [
      { text: 'one', done: true },
      { text: 'two', done: false }
    ]
  },
  methods: {
    toggle: function (item) {
      item.done = !item.done
    }
  }
})
```

当你在表达式处理中想使用原始的DOM事件，你可以通过`$event`来访问：

``` html
<button v-on="click: submit('hello!', $event)">Submit</button>
```

``` js
/* ... */
{
  methods: {
    submit: function (msg, e) {
      e.stopPropagation()
    }
  }
}
/* ... */
```

## `key`过滤器

当你监听键盘消息时，我们常常需要检查键码。Vue.js提供了一个特殊的`key`过滤器，只和`v-on`指示器一起使用。它只带一个键码的参数。

```
<!-- only call vm.submit() when the keyCode is 13 -->
<input v-on="keyup:submit | key 13">
```

它还有几个预设的键码：

```
<!-- same as above -->
<input v-on="keyup:submit | key enter">
```

[完整列表](/api/filters.html#key)检查API文档。

## 为什么在html增加监听?

你可能会担心整个事件监听方法违反了老的好的关于"专注点分离"的规则。放心 - 因为所有的处理函数和表达式都严格的绑定到处理当前View的VM对象，它不会引起任何管理上的困难。事实上，使用`v-on`还有几个好处：

1. 在js代码中撇去html模板更容易的定位处理函数。
2. 因为你不必须手动在js中绑定事件监听，你VM代码和Dom分离了而且是纯逻辑的。它更容易测试。
3. 当一个VM销毁后，所有的事件监听自动删除，你不需要关心自己清除它们。

接下来：[处理表单](/guide/forms.html)。