title: 计算属性
type: guide
order: 8
---

Vue.js的内嵌表达式是很便利的，但是最好的用例是简单的布尔操作或者字符串拼接。更复杂的殴，你应该使用**计算的属性**

在Vue.js，你通过`computed`选项来定义计算属性：

``` js
var demo = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: {
      // the getter should return the desired value
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // the setter is optional
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
})

demo.fullName // 'Foo Bar'
```

当你只需要getter，你可以提供单独的函数而不是一个对象：

``` js
// ...
computed: {
  fullName: function () {
    return this.firstName + ' ' + this.lastName 
  }    
}
// ...
```

一个计算的属性在本质上是定义了getter/setter方法的属性。你可以像普通属性一样来使用计算属性，但是当你访问它，你可以通过getter方法获取值；当你想改变它的值的话，你调用setter方法就可以。

<p class="tip">0.11之前这里有一个依赖集合，当一个条件表达式调用的时候它需要的一个依赖列表。从0.11开始再不需要这样做了</p>

接下来，我们学习怎样[写一个自定义的指示器](/guide/custom-directive.html)。