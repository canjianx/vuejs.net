title: 自定义过滤器
type: guide
order: 10
---

## 基础

和自定义的指示器一样，你可以用全局的`Vue.filter()`方法注册一个自定义的过滤器，传递**过滤器id**和**过滤器函数**。这个过滤器函数需要一个参数然后返回一个过滤过的值。

``` js
Vue.filter('reverse', function (value) {
  return value.split('').reverse().join('')
})
```

``` html
<!-- 'abc' => 'cba' -->
<span v-text="message | reverse"></span>
```

过滤器还接收所有的内嵌参数。

``` js
Vue.filter('wrap', function (value, begin, end) {
  return begin + value + end
})
```

``` html
<!-- 'hello' => 'before hello after' -->
<span v-text="message | wrap before after"></span>
```

## 双向过滤

到现在为止我们使用过滤器来改变从模型中过来的值然后显示到视图中，但是定义一个过滤器反向这个过程也是可以的。

``` js
Vue.filter('check-email', {
  // read is optional in this case, it's here
  // just for demo purposes.
  read: function (val) {
    return val
  },
  // this will be invoked before writing to
  // the model.
  write: function (val, oldVal) {
    return isEmail(val) ? val : oldVal
  }
})
```

## 过滤器上下文


当一个过滤器被调用，它的`this`上下文是被调用的Vue实例。这可以让你来根据Vue实例的状态动态输出结果。

例如:

``` js
Vue.filter('concat', function (value, key) {
  // `this` points to the Vue instance invoking the filter
  return value + this[key]
})
```
``` html
<input v-model="userInput">
<span>{&#123;msg | concat userInput&#125;}</span>
```

通过上面这个简单的例子，我们可以通过一个表达式来获取相同的结果，但是更复杂的需要不同状态的情况下，你需要把他们放到一个计算属性或者自定义过滤器。

内建的`filterBy`和`orderBy`过滤器两个都是不仅仅是对数组有效，他们是根据当前Vue实例的状态来的。

好了！现在是时候学习[组件系统](/guide/components.html)是怎么工作的了。