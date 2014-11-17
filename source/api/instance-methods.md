title: 实例方法
type: api
order: 4
---

## 数据

> 你可以监控Vue实例上的数据。注意所有的监控回调是异步的。另外的，修改值再一个事件循环中批量进行的。这就是说在一个事件循环中一个值修改了多次，回调函数只会带最新的值调用一次。

### vm.$watch( expression, callback, [deep, immediate] )

- **expression** `String`
- **callback( newValue, oldValue )** `Function`
- **deep** `Boolean` *optional*
- **immdediate** `Boolean` *optional*

监控Vue实例的一个表达式修改。可以是单独的属性值或者是一个真实的表达式：

``` js
vm.$watch('a + b', function (newVal, oldVal) {
  // do something
})
```

对象中嵌套的值修改也会被监控，你需要传递第三个`deep`参数为`true`。注意监听数组中的实例不需要做这个。

``` js
vm.$watch('someObject', callback, true)
vm.someObject.nestedValue = 123
// callback is fired
```

给第四个参数`immediate`传递`true`将带当前表达式立刻触发回调函数：

``` js
vm.$watch('a', callback, false, true)
// callback is fired immediately with current value of `a`
```

最后，`vm.$watch`返回一个不监控的函数来阻止触发回调。

``` js
var unwatch = vm.$watch('a', cb)
// later, teardown the watcher
unwatch()
```

### vm.$get( expression )

- **expression** `String`

给定一个表达式在Vue实例上计算返回值，抛出错误的表达式将会被扔掉然后返回`undefined`。

### vm.$set( keypath, value )

- **keypath** `String`
- **value** `*`

给Vue实例上给定的属性设置值。如果属性不存在就创建。

### vm.$add( keypath, value )

- **keypath** `String`
- **value** `*`

给Vue实例（也是它的`$data`对象）增加一个根属性。由于ES5的限制，Vue不能直接监控增加和删除属性，所以当你需要这样的时候使用这个方法和`vm.$delete`。另外，所有监控对象都增加了这两个方法。

### vm.$delete( keypath )

- **keypath** `String`

删除Vue实例（也是它的`$data`对象）的一个根属性。

### vm.$eval( expression )

- **expression** `String`

计算可以包含过滤器的表达式

``` js
// assuming vm.msg = 'hello'
vm.$eval('msg | uppercase') // -> 'HELLO'
```

### vm.$interpolate( templateString )

- **templateString** `String`

计算包含大括号插入的模板字符串片段。注意这个方法只对字符串插入符有效；属性指示器不会被编译。

``` js
// assuming vm.msg = 'hello'
vm.$interpolate('{&#123;msg&#125;} world!') // -> 'hello world!'
```

### vm.$log( [keypath] )

- **keypath** `String` *optional*

把当前的实例数据作为一个普通对象打上日志，它有更多的信息，还可以带一个可选的参数。

``` js
vm.$log() // logs entire ViewModel data
vm.$log('item') // logs vm.item
```

## Events

> 每个vm也是一个时间触发器。当你有多个嵌套的VM，你可以使用事件系统在它们之间沟通。

### vm.$dispatch( event, [args...] )

- **event** `String`
- **args...** *optional*

从当前vm分发一个消息到它的父元素。如果回调函数返回`false`。将会停止传播。

### vm.$broadcast( event, [args...] )

- **event** `String`
- **args...** *optional*

给所以的子VM广播一条消息，如果返回`false`，就不在继续往下广播。

### vm.$emit( event, [args...] )

- **event** `String`
- **args...** *optional*

触发一条消息给自己。

### vm.$on( event, callback )

- **event** `String`
- **callback** `Function`

在当前的VM上监听消息。

### vm.$once( event, callback )

- **event** `String`
- **callback** `Function`

监听一次性消息。

### vm.$off( [event, callback] )

- **event** `String` *optional*
- **callback** `Function` *optional*

如果没有参数，就挺直接监听一切消息；如果只有消息给出，删除所有的回调函数；如果既有消息又有回调，就只删除指定的这个回调。

## DOM

> 所有的操作DOM的方法都和jQuery类似 - 除了还触发Vue.js在`$el`上定义的转化。更多转化的细节请看[增加转化效果](/guide/transitions.html).

### vm.$appendTo( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

把vm的`$el`添加到目标元素上。参数可以是一个元素，也可以是一个选择器字符串。

### vm.$before( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

在目标元素之前插入`$el`。

### vm.$after( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

目标元素之后插入`$el`。

### vm.$remove( [callback] )

- **callback** `Function` *optional*

从Dom中删除`$el`。

## 生命周期

### vm.$mount( [element|selector] )

- **element** `HTMLElement` | **selector** `String` *optional*

如果Vue实例在初始化的时候没有给`el`选项，你可以手动调用`$mount()`指定一个元素给它然后编译。如果没有参数提供，一个空的`<div>`将会自动被创建。在一个已经增加过的实例上调用`$mount`将会没效果。这个方法返回实例自己这样你就可以链式调用其他方法了。

### vm.$destroy( [remove] )

- **remove** `Boolean` *optional*

完全删除VM。清除和其他存在的VS的关系，接触所有指示器的绑定，从DOM中删除`$el`。所有的`$on`和`$watch`监听都会自动被删除。
### vm.$compile( element )

- **element** `HTMLElement`

只编译DOM片段（元素或者文档段）。这个方法返回一个`decompile`函数，这个方法可以卸载这个过程创建的指示器。注意这个卸载方法不删除DOM。这个方法暴露给高级自定义指示器的写法。