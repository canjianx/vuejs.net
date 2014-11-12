title: 处理表单
type: guide
order: 7
---

## 基础

你可以使用`v-model`指示器在表单的输入元素来创建双向绑定。它根据输入的种类来自动选择正确的方式来更新元素。

**例子**

``` html
<form id="demo">
  <!-- text -->
  <p>
    <input type="text" v-model="msg">
    {&#123;msg&#125;}
  </p>
  <!-- checkbox -->
  <p>
    <input type="checkbox" v-model="checked">
    {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}
  </p>
  <!-- radio buttons -->
  <p>
    <input type="radio" name="picked" value="one" v-model="picked">
    <input type="radio" name="picked" value="two" v-model="picked">
    {&#123;picked&#125;}
  </p>
  <!-- select -->
  <p>
    <select v-model="selected">
      <option>one</option>
      <option>two</option>
    </select>
    {&#123;selected&#125;}
  </p>
  <!-- multiple select -->
  <p>
    <select v-model="multiSelect" multiple>
      <option>one</option>
      <option>two</option>
      <option>three</option>
    </select>
    {&#123;multiSelect&#125;}
  </p>
  <p><pre>data: {&#123;$data | json 2&#125;}</pre></p>
</form>
```

``` js
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
```

**结果**

<form id="demo"><p><input type="text" v-model="msg"> {&#123;msg&#125;}</p><p><input type="checkbox" v-model="checked"> {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}</p><p><input type="radio" v-model="picked" name="picked" value="one"><input type="radio" v-model="picked" name="picked" value="two"> {&#123;picked&#125;}</p><p><select v-model="selected"><option>one</option><option>two</option></select> {&#123;selected&#125;}</p><p><select v-model="multiSelect" multiple><option>one</option><option>two</option><option>three</option></select>{&#123;multiSelect&#125;}</p><p>data:<pre style="font-size:13px;background:transparent;line-height:1.5em">{&#123;$data | json 2&#125;}</pre></p></form>
<script>
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
</script>

## 迟更新

默认情况下，`v-model`在每个`input`事件后用数据同步input。你可以增加一个`lazy`属性来改变这种方式成在`change`事件之后更新。

``` html
<!-- synced after "change" instead of "input" -->
<input v-model="msg" lazy>
```

## 转换值到数字

If you want user input to be automatically persisted as numbers, you can add a `number` attribute to your `v-model` managed inputs:

如果你想在input中自动基于数字增加，你可以增加一个`number`属性到你的`v-model`管理的input：

``` html
<input v-model="age" number>
```

## 动态选择选项

如果你需要动态渲染一个`<select>`的选项列表，一种推荐的做法是和`v-model`中使用`options`属性。

``` html
<select v-model="selected" options="myOptions"></select>
```

在你的数据中，`myOptions`应该是一个指向一个作为选项的数组的表达式。这个数组可以包含纯文本，或者一个对象。

对象可以是这样的格式`{text:"", value: ""}`。这需要你在选项中显示的内容和背后的值不一样。

``` js
[
  { text: 'A', value: 'a' },
  { text: 'B', value: 'b' }
]
```

将会渲染成:

``` html
<select>
  <option value="a">A</option>
  <option value="b">B</option>
</select>
```

还有，这个对象还可以是`{ label: "", options: [...]}`。这种情况下它将会被渲染成`<optgroup>`

``` js
[
  { label: 'A', options: ['a', 'b']},
  { label: 'B', options: ['c', 'd']}
]
```

将会渲染成:

``` html
<select>
  <optgroup label="A">
    <option value="a">a</option>
    <option value="b">b</option>
  </optgroup>
  <optgroup label="B">
    <option value="c">c</option>
    <option value="d">d</option>
  </optgroup>
</select>
```

接下来： [计算属性](/guide/computed.html)。