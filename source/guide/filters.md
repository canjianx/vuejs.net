title: 过滤器
type: guide
order: 4
---

## 纲要

Vue.js过滤器本质上是一个函数，它传入一个值，然后处理它，然后返回一个处理过的值。在标记上它用一个单管道表示(`|`)，然后可以带一个或者多个参数。

``` html
<element directive="expression | filterId [args...]"></element>
```

## 例子

过滤器必须放在指示器值最后。

``` html
<span v-text="message | capitalize"></span>
```

你也可以在大括号样式的绑定中使用它们：

``` html
<span>&#123;&#123;message | uppercase&#125;&#125;</span>
```

多过滤器可以链在一起

``` html
<span>&#123;&#123;message | lowercase | reverse&#125;&#125;</span>
```

## 参数

有一些过滤器可以带几个参数。简单的增加用空格分开的参数：

``` html
<span>&#123;&#123;order | pluralize st nd rd th&#125;&#125;</span>
```

``` html
<input v-on="keyup: submitForm | key enter">
```

在[内建过滤器完整列表](/api/filters.html)可以找到上面使用的例子。

现在你知道了指示器和过滤器是什么了，让我开始实践然后试着[显示一个列表](/guide/list.html)。