title: 安装
type: guide
order: 1
vue_version: 0.11.0
dev_size: 167.47
min_size: 52.91
gz_size: 17.27
---

> **Compatibility Note:** Vue.js does 不支持ie8及以下

## 独立使用

只需要下载包含到一个script标签。`Vue`就会被注册成一个全局的变量。

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>开发版本</a><span class="light info">{{dev_size}}kb, 大量的注释以及调试警告信息。</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>产品版</a><span class="light info">{{min_size}}kb 最小化(minified) / {{gz_size}}kb zip压缩的(gzipped)</span>
</div>

也可以使用[cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (需要花一些时间同步所以可能最新的版本还不可用).

## NPM

``` bash
$ npm install vue
# for edge version:
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# only stable version is available through Bower
$ bower install vue
```

## Duo

```js
var Vue = require('yyx990803/vue')
// for edge version:
var Vue = require('yyx990803/vue@dev')
```

## Component

``` bash
$ component install yyx990803/vue
# for edge version:
$ component install yyx990803/vue@dev
```

## AMD 模块加载

独立下载的和使用Bower安装的版本都是用UMD包装过，所以你可以直接当成AMD模块来使用。

## 准备好了么?

[出发吧](/guide/).