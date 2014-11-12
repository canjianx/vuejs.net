title: 显示一个列表
type: guide
order: 5
---

你可以使用`v-repeat`指示器基于VM对象上的数组去重复一个模板元素。为数组中的每个对象，指示器都会创建一个子Vue实例使用这个对象作为它自己的`$data`对象。这些子实例继承了父元素的所有数据，所以在可重复的元素中你可以既访问重复的实例的属性和父实例的属性。另外，你可以访问一个`$index`属性，它和数组中对应的渲染实例的索引对应。

**例子:**

``` html
<ul id="demo">
  <li v-repeat="items" class="item-{&#123;$index&#125;}">
    {&#123;$index&#125;} - {&#123;parentMsg&#125;} {&#123;childMsg&#125;}
  </li>
</ul>
```

``` js
var demo = new Vue({
  el: '#demo',
  data: {
    parentMsg: 'Hello',
    items: [
      { childMsg: 'Foo' },
      { childMsg: 'Bar' }
    ]
  }
})
```

**结果:**

<ul id="demo"><li v-repeat="items" class="item-{&#123;$index&#125;}">{&#123;$index&#125;} - {&#123;parentMsg&#125;} {&#123;childMsg&#125;}</li></ul>
<script>
var demo = new Vue({
  el: '#demo',
  data: {
    parentMsg: 'Hello',
    items: [
      { childMsg: 'Foo' },
      { childMsg: 'Bar' }
    ]
  }
})
</script>

## 原值的数组

对于那些包含了元智的数组，你可以使用简单的`$value`来访问。

``` html
<ul id="tags">
  <li v-repeat="tags">
    {&#123;$value&#125;}
  </li>
</ul>
```

``` js
new Vue({
  el: '#tags',
  data: {
    tags: ['JavaScript', 'MVVM', 'Vue.js']
  }
})
```

**结果:**
<ul id="tags" class="demo"><li v-repeat="tags">{&#123;$value&#125;}</li></ul>
<script>
new Vue({
  el: '#tags',
  data: {
    tags: ['JavaScript', 'MVVM', 'Vue.js']
  }
})
</script>

## 使用标示符

有些时候我们可能想访问更精确的值而不是模糊的到父范围。你可以提供一个参数给`v-repeat`指示器来做这件事。把它当成一个标示符：

``` html
<ul id="users">
  <!-- think of this as "for each user in users" -->
  <li v-repeat="user: users">
    {&#123;user.name&#125;} - {&#123;user.email&#125;}
  </li>
</ul>
```

``` js
new Vue({
  el: '#users',
  data: {
    users: [
      { name: 'Foo Bar', email: 'foo@bar.com' },
      { name: 'John Doh', email: 'john@doh.com' }
    ]
  }
})
```

**结果:**
<ul id="users" class="demo"><li v-repeat="user: users">{&#123;user.name&#125;} - {&#123;user.email&#125;}</li></ul>
<script>
new Vue({
  el: '#users',
  data: {
    users: [
      { name: 'Foo Bar', email: 'foo@bar.com' },
      { name: 'John Doh', email: 'john@doh.com' }
    ]
  }
})
</script>

## 数组变化的方法

Under the hood, Vue.js intercepts an observed Array's mutating methods (`push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()` and `reverse()`) so they will also trigger View updates.

在内部，Vue.js拦截数组的改变方法(`push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()` and `reverse()`)所以他们将会触发视图更新。

``` js
// the DOM will be updated accordingly
demo.items.unshift({ childMsg: 'Baz' })
demo.items.pop()
```

## 增加的方法

Vue.js增加的了两个Array的便利方法`$set()`和`$remove()`。

You should avoid directly setting elements of a data-bound Array with indices, because those changes will not be picked up by Vue.js. Instead, use the agumented `$set()` method:

你应该避免直接使用索引来设置数据绑定的数组，因为这些变化不会被Vue.js监听到。这里需要使用`$set()`方法。

``` js
// same as `demo.items[0] = ...` but triggers view update
demo.items.$set(0, { childMsg: 'Changed!'})
```

`$remove()`只是`splice()`的一个语法糖。它将会根据索引删除一个元素。如果参数不是一个数字，`$remove()`将会搜索数组中的值，然后删除第一个匹配的。

``` js
// remove the item at index 0
demo.items.$remove(0)
```

## 替换数组

如果你不使用修改的方法，例如`filter()`，`concat()` 或者 `slice()`，返回值是一个不同的实例。在这种情况下，你可以用新的来替换旧的数组。

``` js
demo.items = demo.items.filter(function (item) {
  return item.childMsg.match(/Hello/)
})
```

你可能想着将会删除存在的DOM然后重建。但是不是 - Vue.js能识别哪些已经绑定了Vue实例的数组元素，然后尽可能的重用他们。

## 使用`trackby`

在一些情况下，你可能需要替换整个数组 - 例如，从API调用返回。如果你的数据对象有一个唯一的id属性，这是你可以使用一个叫`trackby`的属性给Vue.js一个示意这样它就可以重用存在的有相同id的数据的实例。

例如，如果你的数据像这样:

``` js
{
  items: [
    { _uid: 88f869d, ... },
    { _uid: 7496c10, ... }
  ]
}
```

这时你可以像这样：

``` html
<div v-repeat="items" trackby="_uid">
  <!-- content -->
</div>
```

## 一个对象中的游标

You can also use `v-repeat` to iterate through the properties of an Object. Each repeated instance will have a special property `$key`. For primitive values, you also get `$value` which is similar to primitive values in Arrays.

你也可以使用`v-repeat`来穿越一个对象的属性。每一个重复的实例有一个特殊的属性`$key`。对于原值，你也可以使用`$value`和在数组中一样。

``` html
<ul id="repeat-object">
  <li v-repeat="primitiveValues">{&#123;$key&#125;} : {&#123;$value&#125;}</li>
  <li>===</li>
  <li v-repeat="objectValues">{&#123;$key&#125;} : {&#123;msg&#125;}</li>
</ul>
```

``` js
new Vue({
  el: '#repeat-object',
  data: {
    primitiveValues: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    },
    objectValues: {
      one: {
        msg: 'Hello'
      },
      two: {
        msg: 'Bye'
      }
    }
  }
})
```

**结果:**
<ul id="repeat-object" class="demo"><li v-repeat="primitiveValues">{&#123;$key&#125;} : {&#123;$value&#125;}</li><li>===</li><li v-repeat="objectValues">{&#123;$key&#125;} : {&#123;msg&#125;}</li></ul>
<script>
new Vue({
  el: '#repeat-object',
  data: {
    primitiveValues: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    },
    objectValues: {
      one: {
        msg: 'Hello'
      },
      two: {
        msg: 'Bye'
      }
    }
  }
})
</script>

<p class="tip">在ECMAScript 5没有方法确定啥时候一个对象增加了一个新的属性, 或什么时候删除了一个属性。为了处理这种情况，监控的对象应该增加两个方法：`$add(key, value)` 和 `$delete(key)`. 这些方法可以用来从监控的对象中增加删除属性。同时触发视图更新</p>

## 数组过滤器

有时候我们只需要显示一个数组过滤或者排序过后的版本，而不是修改的源数据，Vue提供了两个内建的过滤去简化用法：`filterBy`和`orderBy`。更多细节请看[文档](/api/filters.html#filterBy)

接下来：[相应消息](/guide/events.html)。
