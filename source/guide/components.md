title: 组件系统
type: guide
order: 11
---

## 使用组件

Vue.js允许你把扩展的Vue子类当曾一个可重用的组件，概念上和[web组件](http://www.w3.org/TR/components-intro/)一样，但是不需要任何填充。为了创建一个组件，只需要使用`Vue.extend()`来创建一个子类的构造函数。

``` js
// Extend Vue to get a reusable constructor
var MyComponent = Vue.extend({
  template: 'A custom component!'
})
```

大部分的Vue的构造函数的参数都能在`Vue.extend()`中使用，但是有两个特例，`data`和`el`。因为每个Vue实例都有自己的`$data`和`$el`，我们当然不希望传递进`Vue.extend()`的值共享给所有的实例。所以当你想定义一个组件应该怎样初始化它的数据和元素时，你应该传递一个函数来代替：

``` js
var ComponentWithDefaultData = Vue.extend({
  data: function () {
    return {
      title: 'Hello!'
    }
  }
})
```

这时，你可以通过`Vue.component()`来**注册**构造函数。

``` js
// Register the constructor with id: my-component
Vue.component('my-component', MyComponent)
```

为了使事情更容易，你可以直接传递一个选项对象而不是一个实际的构造函数。`Vue.component()`将会当你接收到一个对象的时候隐含的为你调用`Vue.extend()`。

``` js
// Note: this method returns the global Vue,
// not the registered constructor.
Vue.component('my-component', {
  template: 'A custom component!'
})
```

这样你可以在父实例的模板中使用一个注册的component(确保这个组件在你的根实例**之前**注册)

``` html
<!-- inside parent template -->
<div v-component="my-component"></div>
```

如果你喜欢，组件也可以使用自定义标签的方式：

``` html
<my-component></my-component>
```

<p class="tip">为了避免和本来的元素的名字冲突，保留了W3C自定义元素规格书里的做法，组件的ID**必须**包含一个连字符`-`</p>

理解`Vue.extend()`和`Vue.component()`之间的不同是很重要的。因为`Vue`本身是个构造函数，`Vue.extend()`是一个**类继承方法**。它的任务是穿件一个`Vue`的子类然后返回它的构造函数。`Vue.component()`，另一方面来说，是一个**内容注册函数**和`Vue.directive()`还有`Vue.filter()`类似。它的任务是关联字符串ID到一个给定的构造函数。所以Vue.js可以从模板中获取它。当直接传递一个选项给`Vue.component()`，它在内部调用`Vue.extend()`。

Vue.js支持两种不同的API范例：基于类的，命令式的，Backbone风格的API，还有基于标记的，说明的，web 组件风格的API。如果你有迷惑的地方，想想怎么通过`new Image()`或者`<img>`标签创建一个图像元素。两种都是有用的，Vue为了最大的灵活性也提供了两种方式。

## 动态组件

你可以通过在`v-compoent`指示器内部使用大括号标签动态的在组件间切换，可以和路由器使用在一起用来做"页面切换":

``` js
new Vue({
  el: 'body',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<div v-component="{&#123;currentView&#125;}">
  <!-- content changes when vm.currentview changes! -->
</div>
```

如果你想保持切换掉的组件保持活动，你可以保存它的状态避免它重渲染，你可以增加一个`keep-alive`的指示器参数：

``` html
<div v-component="{&#123;currentView&#125;}" keep-alive>
  <!-- inactive components will be cached! -->
</div>
```

## 组件的生命周期

每个组件或者Vue对象，都有他们的生命周期：他们将会被创建，编译，插入，分解，最终销毁。实例的每个关键的时刻都会有相应的消息触发，当创建一个实例或者定义一个组件，你可以传递一个生命周期钩子函数用来接收这些消息。例如：

``` js
var MyComponent = Vue.extend({
  created: function () {
    console.log('An instance of MyComponent has been created!')
  }
})
```

[完整的生命周期钩子](/api/options.html#Lifecycle)请查看这里

## 数据层

### 域继承

默认情况下，组件有个**绝对域**。这就意味着你不能再子组件的模板里引用父数据。如果你想穿过去，你需要在你的子组件中使用`inherit: true`选项让他可以继承父数据属性：

``` js
var parent = new Vue({
  data: {
    a: 1
  }
})
// $addChild() is an instance method that allows you to
// programatically create a child instance.
var child = parent.$addChild({
  inherit: true,
  data: {
    b: 2
  }
})
console.log(child.a) // -> 1
console.log(child.b) // -> 2
parent.a = 3
console.log(child.a) // -> 3
```

这里有个警告：因为在Vue实例的数据属性是getter/setter，设置`child.a = 2`将会修改`parent.a`而不是创建一个新的子属性。

``` js
child.a = 4
console.log(parent.a) // -> 4
console.log(child.hasOwnProperty('a')) // -> false
```

### 明确的数据传递

通过绝度域明确的传递数据到子组件，我们可以使用`v-with`指示器。当给了一个单独的不带参数的keypath，对应的父数据中的值会被传递到子组件中，作为它的`$data`。这就意味着传递下来的数据必须是一个对象，它将会覆盖子组件中可能有的默认的`$data`对象。

**例子:**

``` html
<div id="demo-1">
  <p v-component="user-profile" v-with="user"></p>
</div>
```

``` js
// registering the component first
Vue.component('user-profile', {
  template: '{&#123;name&#125;}<br>{&#123;email&#125;}'
})
// the `user` object will be passed to the child
// component as its $data
var parent = new Vue({
  el: '#demo-1',
  data: {
    user: {
      name: 'Foo Bar',
      email: 'foo@bar.com'
    }
  }
})
```

**结果:**

<div id="demo-1" class="demo"><p v-component="user-profile" v-with="user"></p></div>
<script>
  Vue.component('user-profile', {
    template: '{&#123;name&#125;}<br>{&#123;email&#125;}'
  })
  var parent = new Vue({
    el: '#demo-1',
    data: {
      user: {
        name: 'Foo Bar',
        email: 'foo@bar.com'
      }
    }
  })
</script>

`v-with`可以以`v-with="childProp:parentProp"的形式`带一个参数使用。这意味着把`parent[parentProp]`传递到了`child[childPrp]`。注意这种数据继承是单向的：当`parentProp`改变了，`childProp`将会被相应的更新，但是反之不亦然。

**例子:**

``` html
<div id="parent">
  <input v-model="parentMsg">
  <p v-component="child" v-with="childMsg : parentMsg">
    <!-- essentially means "bind `parentMsg` on me as `childMsg`" -->
  </p>
</div>
```

``` js
new Vue({
  el: '#parent',
  data: {
    parentMsg: 'Inherited message'
  },
  components: {
    child: {
      template: '<span v-text="childMsg"></span>'
    }
  }
})
```

**结果:**

<div id="parent" class="demo"><input v-model="parentMsg"><p v-component="child" v-with="childMsg:parentMsg"></p></div>
<script>
new Vue({
  el: '#parent',
  data: {
    parentMsg: 'Inherited message'
  },
  components: {
    child: {
      template: '<span v-text="childMsg"></span>'
    }
  }
})
</script>

## 组件和`v-repeat`

对于对象中的数组，你可以把`v-repeat`和`v-compoent`合并使用。在这种情况下，数组中的每个对象，一个子VM对象将会使用这个对象作为数据而创建，然后指定组件作为构造函数。

``` html
<ul id="demo-2">
  <!-- reusing the user-profile component we registered before -->
  <li v-repeat="users" v-component="user-profile"></li>
</ul>
```

``` js
var parent2 = new Vue({
  el: '#demo-2',
  data: {
    users: [
      {
        name: 'Chuck Norris',
        email: 'chuck@norris.com'
      },
      {
        name: 'Bruce Lee',
        email: 'bruce@lee.com'
      }
    ]
  }
})
```

**结果:**

<ul id="demo-2" class="demo"><li v-repeat="users" v-component="user-profile"></li></ul>
<script>
var parent2 = new Vue({
  el: '#demo-2',
  data: {
    users: [
      {
        name: 'Chuck Norris',
        email: 'chuck@norris.com'
      },
      {
        name: 'Bruce Lee',
        email: 'bruce@lee.com'
      }
    ]
  }
})
</script>

## 子引用

有些是有你需要在javascript中嵌套的访问子组件。要想这样你必须通过`v-ref`访问一个子组件的一个引用id。例如：

``` html
<div id="parent">
  <div v-component="user-profile" v-ref="profile"></div>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// access child component
var child = parent.$.profile
```

当`v-ref`和`v-repeat`一起使用，你得到的值将会是一个包含了镜像到数据数组的子组件数组。

## 事件系统

虽然你能直接访问Vm子和父，但是使用内建的事件系统来跨组件沟通更便捷。它还可以让你的代码更简单和便于管理。一旦一个父子关系确定，你可以使用每个VM的[事件实例方法](/api/instance-methods.html#Events)分派和触发事件。

``` js
var Child = Vue.extend({
  created: function () {
    this.$dispatch('child-created', this)
  }
})

var parent = new Vue({
  template: '<div v-component="child"></div>',
  components: {
    child: Child
  },
  created: function () {
    this.$on('child-created', function (child) {
      console.log('new child created: ')
      console.log(child)
    })
  }
})
```

<script>
var Child = Vue.extend({
  created: function () {
    this.$dispatch('child-created', this)
  }
})

var parent = new Vue({
  el: document.createElement('div'),
  template: '<div v-component="child"></div>',
  components: {
    child: Child
  },
  created: function () {
    this.$on('child-created', function (child) {
      console.log('new child created: ')
      console.log(child)
    })
  }
})
</script>

## 私有内容

有些时候一个组件需要使用一些内容，比如指示器，过滤器，或者它自己的子组件，但是希望保持他们的封装性一遍组件自己在其他地方也可以使用。你可以通过私有内容实例化选项。私有内容只能被所属的组件和它的子组件访问。

``` js
// All 5 types of assets
var MyComponent = Vue.extend({
  directives: {
    // id : definition pairs same with the global methods
    'private-directive': function () {
      // ...
    }
  },
  filters: {
    // ...
  },
  components: {
    // ...
  },
  partials: {
    // ...
  },
  effects: {
    // ...
  }
})
```

可选择的你可以增加私有属性到一个现存的组件构造函数，使用和全局内容注册方法一样的链式API：

``` js
MyComponent
  .directive('...', {})
  .filter('...', function () {})
  .component('...', {})
  // ...
```

## 内容插入

当创建一个可重用的组件的时候，我们经常需要访问和重用宿主元素中的原始内容，它们不是组件的一部分（和Angular中的"transclusion"一样）Vue.js实现了一个内容插入机制，可以兼容现在Web组件草案，使用一个特殊的`<content>`元素来为原始的内容提供一个插入点。

### 单独的插入点

当这里只有一个`<content>`标签并且没有属性，整个原始内同建辉插入到这里。所有插入到`<content>`标签中的内容被认为是**备用内容**。如果宿主元素是空的而且没有内容被插入备用内容将会被显示。例如：

`my-component`的模板:

``` html
<h1>This is my component!</h1>
<content>This will only be displayed if no content is inserted</content>
```

父元素标记使用这个模板:

``` html
<div v-component="my-component">
  <p>This is some original content</p>
  <p>This is some more original content</p>
</div>
```

渲染结果是:

``` html
<div>
  <h1>This is my component!</h1>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</div>
```

### 多插入点

`<content>`元素有一个特殊的属性`select`，它需要一个css选择器。你可以使用不同的`select`属性有多个`<content>`插入点，每一个将会被匹配那个选择器的元素替换。

`multi-insertion-component`的模板:

``` html
<content select="p:nth-child(3)"></content>
<content select="p:nth-child(2)"></content>
<content select="p:nth-child(1)"></content>
```

父元素:

``` html
<div v-component="multi-insertion-component">
  <p>One</p>
  <p>Two</p>
  <p>Three</p>
</div>
```

渲染结果:

``` html
<div>
  <p>Three</p>
  <p>Two</p>
  <p>One</p>
</div>
```

内容插入机制对怎样管理和显示原始内容提供了一个良好的控制，使组件有更大的灵活性和可组合型。

接下来 [应用转变效果](/guide/transitions.html).
