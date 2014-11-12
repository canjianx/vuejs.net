title: 指示器
type: api
order: 6
---

## 反射指示器

> 这些指示器可以把它们自己绑定到Vue实例的一个属性上，或者绑定到一个实例上下文的一个表达式。当绑定的这个值改变，在下一帧到来时这些指示器的`update()`将会被调用。

### v-text

更新元素的 `textContent`.

在文本节点内部, &#123;&#123; Mustache &#125;&#125; 也会被当成一个 `v-text` 指示器

### v-html

更新元素的 `innerHTML`.

<p class="tip">`v-html`和用户提供的数据结合使用是危险的。推荐只有在你能肯定数据源安全的情况下才使用`v-html`，或者给它加一个自定义的过滤器来消除一些潜在的危害。</p>

### v-show

- 这个指示器可以触发转变。

设置元素的css显示属性为`none`或者它原本的值，取决于它绑定的值。

### v-class

- 这个指示器接受一个可选的参数。

如果没有参数，它将会把绑定的值加到元素的class列表，而且当值改变时更新这个class。

如果提供了一个参数，这个参数将会根据绑定的值来决定class的值。和多子句结合是很有用的：

``` html
<span v-class="
  red    : hasError,
  bold   : isImportant,
  hidden : isHidden
"></span>
```

### v-attr

- 这个指示器需要一个参数

更新元素给定的属性（根据指定的参数）。

**Example:**

``` html
<canvas v-attr="width:w, height:h"></canvas>
```

在内部，大括号插入的内容将会编译到计算过的`v-attr`指示器中。

<p class="tip">当设置`<img>`的`src`属性的时候你应该使用`v-attr`代替大括号绑定。你的模板在Vue.js编译之前被浏览器解析，所以当浏览器尝试获取一个图片的URL时大括号绑定将会导致一个404。</p>

### v-style

- 这个指示器接受一个可选参数。

给元素应用内嵌的CSS样式。

当没有元素的时候，Vue.js将会把值设置到`el.style.cssText`.

当有参数的时候，它将会被当曾css属性使用。和多子句联合使用你可以在一起设置多个属性：

**Example:**

``` html
<div v-style="
  top: top + 'px',
  left: left + 'px',
  background-color: 'rgb(0,0,' + bg + ')'
"></div>
```

当这个参数有`$`前缀，Vue.js将会自动应用前一个版本的css规则，所以你不需要手动写所有的前缀。在下面的例子里Vue.js将会应用`transform`, `webkitTransform`, `mozTransform` and `msTransform`：

``` html
<div v-style="$transform: 'scale(' + scale + ')'"></div>
```

<p class="tip">因为IE不管版本在解析html的时候将会删除不起作用的样式所以推荐使用`v-style`而不是用大括号绑定到`style`中。</p>

### v-on

- 需要一个参数。
- 需要值是一个函数或者一个表达式。

在元素上绑定一个消息。消息类别取决于参数。这个是唯一的可以和`key`过滤器结合使用的指示器。 更多信息请看[监听事件](/guide/events.html).

### v-model

- 这个指示器只能用在`<input>`, `<select>` or `<textarea>` 这几个元素.
- 参数: `lazy`, `number`, `options`

给表单的输入元素创建一个双向绑定。默认情况下数据在每个`input`消息中同步。例子的细节请看[处理表单](/guide/forms.html).

### v-if

- 这个指示器可以触发转变。

根据绑定的值有条件的插入或者删除一个元素。如果元素是`<template>`它的内容会作为一个条件块来提取。

**例子:**

``` html
<template v-if="test">
  <p>hello</p>
  <p>world</p>
</template>
```

将会渲染成:

``` html
<!--v-if-start-->
<p>hello</p>
<p>world</p>
<!--v-if-end-->
```

### v-repeat

- 创建一个子Vue实例。
- 要求值是数组或者对象。
- 可以触发转变。
- 接受一个可选参数。
- 指示器参数: `trackby`

为每一个绑定到数组的项创建一个子VM对象。当修改方法调用的时候这些子VM对象将会被自动的创建和删除，例如数组中调用`push()`。

如果没有提供参数，这个子VM对象将会直接使用数组中的元素作为它的`$data`。如果这个值不是一个对象，一个包装的数据对象将会被创建而且将会把值设置到一个名为`$value`的属性上。

**例子:**

``` html
<ul>
  <li v-repeat="users">
    {&#123;name&#125;} {&#123;email&#125;}
  </li>
</ul>
```

如果提供一个参数，包装的数据对象也会创建，使用参数作为键的名字。这就可以在模板中访问更多的属性。

``` html
<ul>
  <li v-repeat="user : users">
    {&#123;user.name&#125;} {&#123;user.email&#125;}
  </li>
</ul>
```

更细的例子请看：[显示一个列表](/guide/list.html).

### v-with

- 只和`v-component`在一起使用。
- 只接受单值，不接受表达式。

允许子VM对象从父对象那里继承数据。你可以传递一个对象进去做为`data`，或者绑定父对象的一个属性。必须和`v-component`结合使用。

The data inheritance is one-way: when parent property changes, the child will be notified of the change and update accordingly; however if the child sets the property to something else, it will not affect the parent, and the modified property will be overwritten the next time parent property changes.

数据继承是一种方法：当父属性改变了，子对象将会收到修改通知然后做相应的更新；然而，如果子对象设置了这个值，将不会影响父元素，当下次父元素修改的时候这个修改的值又会被覆盖。

继承对象的例子:

``` js
// parent data looks like this
{
  user: {
    name: 'Foo Bar',
    email: 'foo@bar.com'
  }
}
```

``` html
<my-component v-with="user">
  <!-- you can access properties without `user.` -->
  {&#123;name&#125;} {&#123;email&#125;}
</my-component>
```

继承独立属性的例子 (使用相同的数据):

``` 
<my-component v-with="myName: user.name, myEmail: user.email">
  <!-- you can access properties with the new keys -->
  {&#123;myName&#125;} {&#123;myEmail&#125;}
</my-component>
```

## 字面量指示器

> 字面量指示器把他们的属性值当成纯文本；他们不尝试去把它们绑定到任何元素上。它们所做的事情是使用这个字符串值执行`bind()`函数。字面量指示器在它值里面接受大括号表达式，但是这个表达式只是在第一次编译的时候执行，也不会对数据变化作出反应。

### v-component

- 指示器参数: `keep-alive`

用注册的组件作为子VM对象编译这个元素。可以使用`v-with`从父对象继承数据。更多信息请看[组件系统](/guide/components.html).

### v-ref

在父对象上注册一个子对象的引用为了更方便的访问。只和`v-component`和`v-repeat`联合使用。组件实例将会被它的父元素使用`$`来访问，例子请看[子对象引用](/guide/components.html#Child_Reference).

当和`v-repeat`一起使用，这个值将会是一个包含了所有对应的子VM实例的数组。

### v-el

注册一个DOM元素的引用到它对应的Vue对象上，为了方面访问。例如`<div v-el="hi">` 将会用`vm.$$.hi`来访问这个元素。

### v-partial

使用注册的片段来替换元素的innerHTML。可以使用`Vue.partial()`来注册片段或者传入`partials`参数。

在`v-partail`使用大括号可以使它反射：

``` html
<!-- content will change based on vm.partialId -->
<div v-partial="{&#123;partialId&#125;}"></div>
```

你也可以这种语法（不支持反射）：

``` html
<div>&#123;&#123;> my-partial&#125;&#125;</div>
```

### v-transition

通知Vue.js应用转化到元素上。这个转化类将在某些转换触发指示器修改元素或者Vue实例的DOM操作方法调用时应用。

细节请看 [转变指南](/guide/transitions.html).

## 空指示器

> 空指示器不要求也会忽略他们的属性值。

### v-pre

跳过这个元素以及子元素的编译。跳过大部分的没有指示器的节点可以提高编译速度。

### v-cloak

这个属性一直保持到相关的VM对象完成编译。和一些CSS规则一起使用比如`[v-cloak] {display: none}`，这个指示器用来隐藏没有编译的大括号绑定直到VM准备好。
