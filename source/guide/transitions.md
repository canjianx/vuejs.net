title: 转变
type: guide
order: 12
---

通过Vue.js的转变系统，你可以在元素插入和删除时应用自动转变效果。这里有两个选项要做：通过转变/动画定义css类，或者注册一个定义了自定义javascript钩子函数的对象。

使用`v-transition="my-transition"`指示器，Vue将会：

1. 试着找通过`Vue.transition(id, def)`注册的javascript转变**定义**，或者传递一个`transitions`选项，通过`my-transition`的id。如果发现，将会使用这个**定义** 

2. 如果没找到，它会探测目标元素是否有转换和动画应用，然后在合适的时间增加和删除css类。

3. 如果没有指定转变和动画，DOM管理将会在下一帧中执行。

<p class="tip">所以的Vue.js转变都只会vue.js管理的Dom操作中触发, 即使是内建的指示器, 例如 `v-if`, 或者内建的方法, 例如 `vm.$appendTo()`.</p>


## CSS 转变

特别的CSS转变期像这样：

``` html
<p class="msg" v-if="show" v-transition="expand">Hello!</p>
```

你还需要为`.expend-enter`和`.expend-leave`类定义css规则：

``` css
.msg {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.msg.expand-enter, .msg.expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
```

<div id="demo"><p class="msg" v-if="show" v-transition="expand">Hello!</p><button v-on="click: show = !show">Toggle</button></div>

<style>
.msg {
  transition: all .5s ease;
  height: 30px;
  background-color: #eee;
  overflow: hidden;
  padding: 10px;
  margin: 0 !important;
}
.msg.expand-enter, .msg.expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
</style>

<script>
new Vue({
  el: '#demo',
  data: { show: true }
})
</script>

这些类的切换基于你的`v-transition`指示器。在`v-transition="fade"`的例子中，这个类将会在`.fade-enter`和`.fade-leave`间切换。当没有提供值，它们默认的将会是`.v-enter`和`.v-leave`。

When the `show` property changes, Vue.js will insert or remove the `<p>` element accordingly, and apply transition classes as specified below:

当`show`属性改变，vue.js将会插入删除`<p>`，然后像下面应用转变类：

- 当`show`变成false，Vue.js将会：
  1. 应用`v-leave`类到元素上，并触发转变；
  2. 等转变完成；（监听转变消息）
  3. 从Dom中删除元素并且删除`v-leave`元素。

- 当`show`变成true, Vue.js将会：
  1. 应用`v-enter`到元素上；
  2. 把它插入到dom;
  3. 强制一个css重排这样`v-enter`会被真正应用；
  4. 删除`v-enter`类然后触发转变回元素原来的状态。

<p class="tip">当多元素在一起转变, Vue.js会批量执行并只做一次重排操作</p>

## CSS 动画

CSS动画和CSS转变一样的过程，不同点在于`v-enter`不是在元素插入后立刻删除，而是在`animationend`回调中。

**例子:** (这里省略了css前缀)

``` html
<p class="animated" v-if="show" v-transition="bounce">Look at me!</p>
```

``` css
.animated {
  display: inline-block;
}
.animated.bounce-enter {
  animation: bounce-in .5s;
}
.animated.bounce-leave {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```

<div id="anim" class="demo"><span class="animated" v-if="show" v-transition="bounce">Look at me!</span><br><button v-on="click: show = !show">Toggle</button></div>

<style>
  .animated {
    display: inline-block;
  }
  .animated.bounce-enter {
    -webkit-animation: bounce-in .5s;
    animation: bounce-in .5s;
  }
  .animated.bounce-leave {
    -webkit-animation: bounce-out .5s;
    animation: bounce-out .5s;
  }
  @keyframes bounce-in {
    0% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
  }
  @keyframes bounce-out {
    0% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
  }
  @-webkit-keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
    }
  }
  @-webkit-keyframes bounce-out {
    0% {
      -webkit-transform: scale(1);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(0);
    }
  }
</style>

<script>
new Vue({
  el: '#anim',
  data: { show: true }
})
</script>

## JavaScript 函数

下面的例子使用jquery来注册一个自定义的javascript转变定义：

``` js
Vue.transition('fade', {
  beforeEnter: function (el) {
    // a synchronous function called right before the
    // element is inserted into the document.
    // you can do some pre-styling here to avoid
    // FOC (flash of content).
  },
  enter: function (el, done) {
    // element is already inserted into the DOM
    // call done when animation finishes.
    $(el)
      .css('opacity', 0)
      .animate({ opacity: 1 }, 1000, done)
    // optionally return a "cancel" function
    // to clean up if the animation is cancelled
    return function () {
      $(el).stop()
    }
  },
  leave: function (el, done) {
    // same as enter
    $(el).animate({ opacity: 0 }, 1000, done)
    return function () {
      $(el).stop()
    }
  }
})
```

这样你就可以通过指定一个转变id给`v-transition`来使用它了。注意这比css转变有更高的优先级。

``` html
<p v-transition="fade"></p>
```

接下来：[构建大型程序](/guide/application.html)。