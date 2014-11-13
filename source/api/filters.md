title: 过滤器
type: api
order: 7
---

### capitalize

*'abc' => 'Abc'*

### uppercase

*'abc' => 'ABC'*

### lowercase

*'ABC' => 'abc'*

### currency

- 可以带一个可选参数

*12345 => $12,345.00*

可以传入一个符号 (默认是 $).

### pluralize

- 至少有一个参数

基于过滤的值来使参数复数化。如果只有一个参数，复数化的形式只是简单的加一个"s"。 如果有一个以上的参数，那么参数将会当成相对应的一个，两个，三个等等复数化的形式相对应的数组。如果复数化的内容超过了参数长度，它将使用数组中的最后一个值。

**例子:**

``` html
{&#123;count&#125;} {&#123;count | pluralize item&#125;}
```

*1 => '1 item'*  
*2 => '2 items'*

``` html
{&#123;date&#125;}{&#123;date | pluralize st nd rd th&#125;}
```

结果是:

*1 => '1st'*  
*2 => '2nd'*
*3 => '3rd'*
*4 => '4th'*
*5 => '5th'*

### key

- 这个过滤器只工作在`v-on`里
- 只有并只能有一个参数

当键值匹配上可以调用一个包装的处理函数。你还可以使用几个字符串命名的常用的键：

- enter
- tab
- delete
- esc
- up
- down
- left
- right

**例子:**

``` html
<input v-on="keyup:doSomething | key enter">
```

`doSomething` 会在回车按下后调用执行。

### filterBy

**Syntax:** `filterBy searchKey [in dataKey]`.

- 只在`v-repeat`中有效
- 是一个计算的过滤器

使`v-repeat`只显示一个源数组的过滤值。`searchKey`参数是一个在VM上下文的一个属性键，这个属性值将会被当成寻找对象：

``` html
<input v-model="searchText">
<ul>
  <li v-repeat="users | filterBy searchText">{&#123;name&#125;}</li>
</ul>
```

如果使用了这个过滤器，它将会在数组中的每个元素上递归的寻找`searchText`的值。例如，如果一个元素是`{ name: "Jack", phone: "555-123-4567"}`而且`searchText`有值`'555'`，这个元素就可以认为是匹配上了。

你也可以使用`in dateKey`参数缩小范围：

``` html
<input v-model="searchText">
<ul>
  <li v-repeat="users | filterBy searchText in name">{&#123;name&#125;}</li>
</ul>
```

现在，只会去用`searchText`的值去匹配元素的`name`属性。所以`searchText`是`"555"`的将不会匹配上，但是`"Jack"`还是会匹配上。

最后，你可以使用引号表示文本参数：

``` html
<ul>
  <li v-repeat="users | filterBy '555' in 'phone'">{&#123;name&#125;}</li>
</ul>
```

### orderBy

**Syntax:** `orderBy sortKey [reverseKey]`.

- 只在`v-repeat`有效
- 是一个计算的过滤器

排序`v-repeat`的显示结果。`sortKey`参数是VM上下文的一个属性键。它的值将会被用来排序。可选择的`reverseKey`也是一个属性键，但是这个值决定要不要翻转结果。

``` html
<ul>
  <li v-repeat="users | orderBy field reverse">{&#123;name&#125;}</li>
</ul>
```

``` js
new Vue({
  /* ... */
  data: {
    field: 'name',
    reverse: false
  }
})
```

你也可以使用引号表示文本参数。使用`-1`表示文本的翻转：

``` html
<ul>
  <li v-repeat="users | orderBy 'name' -1">{&#123;name&#125;}</li>
</ul>
```
