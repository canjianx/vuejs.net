title: 指示器
type: guide
order: 3
---

## 概要

如果你之前没有用过AngularJS，那么你可能不知道什么是指示器。本质上，指示器是一些特殊的标记用来告诉库对一个DOM元素做一些事情。在Vue.js，只是的概念是比Angular彻底的简化。一个Vue.js的指示器只是出现在HTML前置属性中的，例如：

``` html
<element
  prefix-directiveId="[argument:] expression [| filters...]">
</element>
```

## 简单的例子

``` html
<div v-text="message"></div>
```

这里前缀`v`是默认的。这个指示器的ID是`text`而且表达式是`message`。这个指示器告诉Vue.js当Vue实例`message`属性修改的时候，更新这个div的`textContent`。

## 内部表达式

``` html
<div v-text="'hello ' + user.firstName + ' ' + user.lastName"></div>
```

这里我们使用了可以计算的表达式代替单独的属性值。vue.js自动跟踪表达式依赖的属性。多亏了异步批量更新，即使当有多个依赖更新，表达式只会在一个事件周期更新一次。

你需要小心用表达式，避免放太多的逻辑到你的模板中，特别带有副作用的语句(事件监听者表达式的异常)。为了阻止在模板中太多的使用逻辑，Vue.js内部的表达式限制在**一个语句**。需要更加复杂的操作的绑定，使用[计算的属性](/guide/computed.html)代替。

<p class="tip">为了安全的原因，在内部的表达式中你只能访问当前Vue实例上下文的属性和方法和它父元素的。</p>

## 参数

``` html
<div v-on="click : clickHandler"></div>
```

有些指令在表达式或者关键路径之前需要一个参数。在这个例子里`click`参数指示我们想`v-on`指示器去监听一个点击事件，然后调用ViewModel实例的`clickHandler`方法。

## 过滤器

过滤器可以附加在表达式或者关键路径指示器后在更新DOM之前处理值。过滤器使用单向管道(`|`)表示类似shell脚本。更多信息请看[深入过滤器](/guide/filters.html)。

## 多子句

你可以在一个属性中创建一个指示器的多绑定，用逗号隔开。下面的钩子就是多指示器实例。

``` html
<div v-on="
  click   : onClick,
  keyup   : onKeyup,
  keydown : onKeydown
">
</div>
```

## 指示器字面量

一些指示器不创建数据绑定 - 它们简单的去属性的字面量。例如`v-component`指示器：

``` html
<div v-component="my-component"></div>
```

这里`"my-component"`不是一个数据属性 - 这是Vue.js使用的字串id来查找匹配的组件构造函数。

你也可以使用大括号表达式代替字面量。例如，下面的代码允许你动态的处理你想使用的组件的种类：

``` html
<div v-component="{&#123; isOwner ? 'owner-panel' : 'guest-panel' &#125;}"></div>
```

当大括号里面的表达式改变了，关联的组件也会做相应的渲染。

然而，注意`v-component`和`v-partial`是仅存的有双向绑定行为的字面量指示器，其他字面量指示器里的大括号表达式，例如`v-ref`，是只求值**一次**。当指示器被编译后，它就不在相应值的改变了。

字面量指示器的全列表可以在这里找到[API文档](/api/directives.html#Literal_Directives)。

## 空指示器

有一些控制器不需要属性值 - 它们只是简单的对一个元素做一些事，只做一次，例如`v-pre`指示器：

``` html
<div v-pre>
  <!-- markup in here will not be compiled -->
</div>
```

所有的空指示器列表可以在[API文档](/api/directives.html#Empty_Directives)找到。

接下来，让我讨论[过滤器](/guide/filters.html)。