title: 实例属性
type: api
order: 3
---

### vm.$el

- **Type:** `HTMLElement`
- **Read only**

Vue属性管理的Dom元素。

### vm.$data

- **Type:** `Object`

Vue实例监控的数据对象。你可以使用一个新的对象包装它。Vue实例代理访问它的数据对象的属性。

### vm.$options

- **Type:** `Object`

当前Vue对象中使用的实例选项。当你想包含自定义的属性的时候是有用的。

``` js
new Vue({
  customOption: 'foo',
  created: function () {
    console.log(this.$options.customOption) // -> 'foo'
  }
})
```

### vm.$parent

- **Type:** `Vue`
- **Read only**

父实例，如果有的话。
### vm.$root

- **Type:** `Vue`
- **Read only**

当前组件树中的根Vue实例。如果当前实例没有父实例，这个值将会是自己。

### vm.$

- **Type:** `Object`
- **Read only**

注册了`v-ref`后一个包含子组件的一个对象。更多细节看[v-ref](/api/directives.html#v-ref)。

### vm.$$

- **Type:** `Object`
- **Read only**

注册了`v-el`后一个包含了DOM元素的一个对象。更多信息看[v-el](/api/directives.html#v-el)。

### Meta Properties

Instances created by `v-repeat` will also have some meta properties, e.g. `vm.$index`, `vm.$key` and `vm.$value`. For more details, see [the guide on using `v-repeat`](/guide/list.html).

被`v-repeat`创建的实例也包含了一些元属性，例如`vm.$index`，`vm.$key`和`vm.$value`。更多信息看[使用`v-repeat`入门](/guide/list.html)。