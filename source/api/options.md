title: 选项
type: api
order: 2
---

## 数据

### data

- **Type:** `Object | Function`
- **Restricton:** Only accepts `Function` when used in `Vue.extend()`.

Vue实例的数据对象，可以用`vm.$data`访问。

```js
var data = { a: 1 }
var vm = new Vue({
  data: data
})
vm.$data === data // -> true
```

Vue实例将会代理访问它的所有属性，所以你可以在Vue实例上管理这些属性，然后这些改变会同步到实际的对象上：

```js
vm.a   // -> 1
vm.a = 2
data.a // -> 2
data.a = 3
vm.a   // -> 3
```

对象必须是遵守JSON规范的（不包含环形引用）。你可以像普通对象一样使用它，你通过`JSON.stringify`串行化得到相同的值。你也可以在多个Vm实例间共享它。

在`Vue.extend()`中使用`data`选项时，有一个特殊情况。因为你不想在继承的构造器里共享所有嵌套的对象，你必须提供一个函数来返回默认数据对象的一个拷贝。

``` js
var MyComponent = Vue.extend({
  data: function () {
    return {
      message: 'some default data.',
      object: {
        fresh: true
      }
    }
  }
})
```

<p class="tip">在背后，Vue.js附加一个隐藏的属性`__ob__`和转换数据的可枚举属性到gettersHe setters以便可以收集依赖。以`$`和`_`开头的将会被跳过。</p>

### methods

- **Type:** `Object`

混合进vue实例的方法，你可以直接访问VM实例上的方法，或者在表达式里面直接使用。每个函数的上下文关系`this`都绑定在Vue实例上。

**例子:**

```js
var vm = new Vue({
  data: { a: 1 },
  methods: {
    plus: function () {
      this.a++
    }
  }
})
vm.plus()
vm.a // 2
```

### computed

- **Type:** `Object`

计算的属性也被包含进Vue实例。所有的getter和setters都有他们自己的`this`来自动绑定到Vue实例的上下文。

**Example:**

```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // get only, just need a function
    aDouble: function () {
      return this.a * 2
    },
    // both get and set
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
})
vm.aPlus   // -> 2
vm.aPlus = 3
vm.a       // -> 2
vm.aDouble // -> 4
```

### paramAttributes

- **类别:** `Array`

设置在Vue实例上的属性名称的一个数组作为初始数据。给一个组件传递值的时候是很有用的。

**例子:**

``` js
Vue.component('param-demo', {
  paramAttributes: ['size', 'message'],
  compiled: function () {
    console.log(this.size)    // -> 100
    console.log(this.message) // -> 'hello!'
  }
})
```

``` html
<param-demo size="100" message="hello!"></param-demo>
```

参数属性可以包含插入标签。这个插入将不在父对象的环境计算，而是在[`v-with`](/api/directives.html#v-with)之下计算，这就意味着如果插入表达式修改了，组件相关联的属性也会被更新：

``` html
<param-demo message="{&#123;parentMessage&#125;}"></param-demo>
```

当使用带连字符的属性的`paramAttributes`有个特殊情况：

1. 如果属性是一个数据属性，`data-`前缀会自动去掉；

2. 如果属性还包含破折号，它将会被驼峰化，这是因为在模板中访问带破折号的顶级属性是不方便的：表达式`my-param`将会解析成一个减法操作的表达式除非你使用这种语法`this['my-param']`。

这意味着一个参数属性`data-hello`将会被设置在vm的`vm.hello`对象上；然后`my-param`将会变成`vm.myParam`。

## DOM

### el

- **类别:** `String | HTMLElement | Function`
- **限制:** 使用`Vue.extend()`时只接受`Funciton`类型。

给Vue实例提供一个已存在的DOM元素。它可以是一个CSS选择器字符串，一个真正的html元素，或者一个返回一个html元素的函数。这个值将会用`vm.$el`访问。

当使用`Vue.extend`，一个函数必须被提供，这样每个实例可以单独的创建元素。

如果是在一个插入表达式里面，这个实例将会立刻进入编译；否则，用户必须手动的通过`vm.$mount()`来启动编译。

### template

- **类别:** `String`

将会被插入到`vm.$el`的字符串模板。任何在`vm.$el`内的标示符都会被覆盖，除非这个模板中有[内容插入点](/guide/components.html#Content_Insertion)。如果选项**replace**设置成`true`，模板将会完全的替换`vm.$el`。

如果以`#`开头，它将会被当成是一个选择器然后使用选择出来的元素的innerHTML作为模板字符串。这允许使用普通的`<script type="x-template">`来包含模板。

<p class="tip">Vue.js使用基于Dom的模板。编译器遍历DOM元素去寻找指示器然后创建绑定。这意味着所有的Vue.js模板是可以被浏览器解析成实际的DOM元素的html Vue.js把模板字符串转换成DOM片段这样它可以在创建更多的Vue实例的时候通过克隆来完成，如果你想你的模板是有效的html， 你可以通过配置指示器前缀为`data-`来达到。</p>

### replace

- **类别:** `Boolean`  
- **默认:** `false`
- **限制:** 当**template**有效的时候有效。

是否用模板内容替换原始的`vm.$el`。

## 生命周期

所有的生命周期钩子的`this`都绑定到它的Vue实例上。Vue实例将会为每一个`"hook:<hookName>"`形式的钩子触发相关的事件。例如为`created`触发一个`hook:created`。

### created

- **Type:** `Function`

在实例被创建的时候同步调用。在这个阶段，实例完成了包含以下内容的预处理：数据健康，计算属性，方法，监控事件回调。然而，dom编译还没开始，`$el`还没有效。

### beforeCompile

- **Type:** `Function`

在编译之前调用。

### compiled

- **Type:** `Function`

编译完成后调用，在这个阶段，所有的指示器都绑定成功，所以数据变化将会触发DOM更新。然而，`$el`不保证已经插入到文档中了。
### ready

- **Type:** `Function`

当完成编译**而且**`$el`也第一次的插入到文档中了之后调用。注意这个插入必须要通过Vue完成的(通过例如`vm.$appendTo()`的方法或者是一个指示器更新的结果)来触发的`ready`钩子。

### attached

- **Type:** `Function`

当`vm.$el`被指示器或者vm实例方法如`$appendTo()`调用插入到DOM中时调用。直接操作`vm.$el`不会触发这个钩子。

### detached

- **Type:** `Function`

当指示器或者vm实例方法调用从DOM中删除时调用。直接操作`vm.$el`不会触发这个钩子。

### beforeDestroy

- **Type:** `Function`

在Vue实例销毁之前调用。在这个阶段这个实例还是有完整的功能的。

### destroyed

- **Type:** `Function`

当一个Vue实例已经被销毁之后调用。当这个钩子被调用所有的指示器绑定已经解绑，所有的子Vue实例已经被销毁。

注意如果有一个leaving变化，`destroyed`狗仔在变化完成**之后**调用。

## Assets

这里有一些Vue实例和它的子实例在编译期有效的私有的资源。

### directives

- **Type:** `Object`

对Vue实例有效的指示器的哈希。更多怎么写一个自定义的指示器看[写自定义的指示器](/guide/custom-directive.html)。

### filters

- **Type:** `Object`

对Vue实例有效的过滤器的哈希。更多怎么写一个自定义的过滤器，请看[写自定义的过滤器](/guide/custom-filter.html)。

### components

- **Type:** `Object`

对Vue实例有效的组件的哈希。更多怎么扩展一个组件看[组件系统](/guide/components.html)。

### partials

- **Type:** `Object`

对Vue实例有效的片段的哈希。请看[v-partial](/api/directives.html#v-partial)。

### transitions

- **Type:** `Object`

对Vue实例有效的转化的一个实例。更多细节看入门[转变](/guide/transitions.html)。

## Others

### inherit

- **类别:** `Boolean`
- **默认:** `false`

是否继承父的上下文数据。如果你想创建一个继承父上下文数据的设置成`true`。当`inherit`设置成`true`的时候你可以：

1. 把父上下文属性绑定到组件模板里；
2. 在组件实例里直接访问父属性，通过原型继承。

当使用`inherit: true`时有一件很重要的事情是**子可以设置父属性**，因为虽有的vue实例属性是getter/setters。

**例子:**

``` js
var parent = new Vue({
  data: { a: 1 }
})
var child = parent.$addChild({
  inherit: true,
  data: { b: 2 }
})
child.a  // -> 1
child.b  // -> 2
// the following line modifies parent.a
// instead of creating a new property on child:
child.a = 2
parent.a // -> 2
```

### events

一个对象键是监听的时间，值是相关的回调函数。注意这里的Vue事件不是DOM事件。值也可以是一个方法名。vue实例将会为实例中的每个对象调用`$on()`。

**例子:**

``` js
var vm = new Vue({
  events: {
    'hook:created': function () {
      console.log('created!')
    },
    greeting: function (msg) {
      console.log(msg)
    },
    // can also use a string for methods
    bye: 'sayGoodbye'
  },
  methods: {
    sayGoodbye: function () {
      console.log('goodbye!')
    }
  }
}) // -> created!
vm.$emit('greeting', 'hi!') // -> hi!
vm.$emit('bye')             // -> goodbye!
```

### watch

- **Type**: `Object`

对象键是监控的对象，值是对应的回调函数。值可以是方法名。Vue实例将会为实例中每个对象调用`$watch()`。

**例子:**

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  watch: {
    'a': function (val, oldVal) {
      console.log('new: %s, old: %s', val, oldVal)
    }
  }
})
vm.a = 2 // -> new: 2, old: 1
```

### mixins

- **Type**: `Array`

`mixins`选项棘手一个混入的对象。这些混入的对象可以像包含普通对象一样包含一个实例对象，根据最后的选项合并使用的合并逻辑和`Vue.extend()`一样。例如如果你混入的对象包含一个创建的钩子然后这个组件也有一个。两个函数将都会被调用。

**例子:**

``` js
var mixin = {
  created: function () { console.log(2) }
}
var vm = new Vue({
  created: function () { console.log(1) },
  mixins: [mixin]
})
// -> 1
// -> 2
```

### name

- **类别**: `String`
- **限制:** 在 `Vue.extend()`中使用.

在控制台检查一个扩展的Vue组件的时候，默认的构造函数的名字是`VueComponent`，这不是很有意义。传递一个`name`参数给`Vue.extend()`，你将会得到一个更好的检查输出，你可以知道你正在看哪个组件。这个字符串将会被驼峰化并作为组件的构造函数的名字使用。

**例子:**

``` js
var Ctor = Vue.extend({
  name: 'cool-stuff'
})
var vm = new Ctor()
console.log(vm) // -> CoolStuff {$el: null, ...}
```