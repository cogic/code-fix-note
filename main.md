* #elementui

el-form-item的属性prop通常为固定字符串，如 `prop=“userName”` 或 `prop="user.Name"` 。但有时需要动态决定，可以这样写

```
<el-form-item
  v-for="(user, index) in form.users"
  :prop="`users[${index}].value`"
>
...
```



* #git

从远程仓库拉取代码 -> 修改代码 -> 本地commit --amend -> 尝试 push到远程仓库时失败

正确操作：推送到远程仓库时使用 `git push --force-with-lease origin master`  ，即push时加上 `--force-with-lease` 

[git-commit-amend踩坑_路痴的兔子的博客-CSDN博客](https://blog.csdn.net/qq_37284607/article/details/118547987)

[git commit --amend踩坑记录_无色云的博客-CSDN博客](https://blog.csdn.net/weixin_38669561/article/details/103385514)



* #vue #css #animation 

vue 单文件的 `<style scoped>`中写了 @keyframes，然后设置 animation 动画。但实际无效果。

解决方法：将 @keyframes 放入没有 ‘scoped’ 的 style 标签中（因为没有scoped，所以相对于是全局引入的。因此可以直接选个合适的地方全局引入即可）

[ vue中动态修改animation效果无效问题_九州白的博客-CSDN博客_动态设置animation](https://blog.csdn.net/qq_38783557/article/details/124061163)

[Vue 项目使用 css3 动画无效 （不去掉 scoped 的简单解决方法）_未来w的博客-CSDN博客_vue动画不生效](https://blog.csdn.net/Farewell_w/article/details/114311594)



* #css

页面因功能需要而出现滚动条嵌套，如页面整体有纵向滚动条，页面内部一个盒子也有纵向滚动条。此时在内部盒子中使用滚轮，滚动到底部或顶部时继续滚动，会带动页面的整体滚动，有时不希望这样。

解决方法：为需要独立滚动的盒子添加css属性`overscroll-behavior: contain;`

[overscroll-behavior - CSS: Cascading Style Sheets | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/CSS/overscroll-behavior)

[滚动嵌套时怎么防止内外层互相影响？ - 掘金 (juejin.cn)](https://juejin.cn/post/7057884355691020301)



* #vue #vue-cli #sass #scss

现有 variables.scss 存放全局变量，但无论在 main.js 还是 App.js 等文件中引入都无法在其他文件中直接使用变量，仍需要在每个文件中单独引入，十分不便。

解决方法：对于用 vue-cli 创建的项目，可在 vue.config.js 文件中进行如下配置

```javascript
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      scss: {
        additionalData: `@import "~@/assets/styles/variables.scss";`, //具体路径根据实际修改
      },
    },
  },
}
```

[CSS 相关 | Vue CLI (vuejs.org)](https://cli.vuejs.org/zh/guide/css.html#向预处理器-loader-传递选项)



* #vue #vue-router

VueRouter报错：Navigation cancelled from ... to ... with a new navigation.

问题原因：这种情况可能是连续指向两次以上的路由跳转导致.比如原来在 /a，使用router跳转到/b，然后又跳转到/c。由于/a还没跳转到/b就立刻又跳转/c，遂有一次跳转‘cancelled ’。这个报错见与vue-router 3.x的版本，4.x可能不报错。

解决办法：出现这种情况说明有不必要的路由跳转，最好是改善代码。也有其他解决办法的说法，未必好用。

[vue-router 报错：Navigation cancelled from“/...“ to “/...“ with a new navigation._迎面吹来微风的博客-CSDN博客](https://blog.csdn.net/qq_42185724/article/details/112219867)

[vue-router 报错：Navigation cancelled from“/...“ to “/...“ with a new navigation._meloeyLeo的博客-CSDN博客](https://blog.csdn.net/weixin_47084275/article/details/108205775)

[解决 Uncaught (in promise) Error: Navigation cancelled from “/...“ to “/...“ with a new navigation._无语，人生的过客� � �的博客-CSDN博客](https://blog.csdn.net/li22356/article/details/115766636)



* #vue #this

当前建有 debounce.js 防抖函数脚本，将 debounce 函数引入 .vue 文件后使用发现报错：

```javascript
// test 为需要 debounce 的函数
export default {
  methods: {
    test() {
      console.log('test');
    },
    dtest: debounce(this.test, 1000),
  },
}
// TypeError: Cannot read properties of undefined (reading 'test')
```

原因是这种情况下 `this` 是取不到的（为 undefined）

解决办法：

方法①（不推荐） ：不使用 this 可以避免这个问题：

```javascript
export default {
  methods: {
    test() {
      console.log('test');
    },
    dtest: debounce(function () {
      // 内部如果使用 this 的话是没有问题的，这与 debounce 实现相关
      console.log('test');
    }, 1000),
  },
}
```

上面的方式直接将函数整体作为参数，缺点是如果函数内容比较多则代码不够清晰。且如果上面的 test 函数既有需要 debounce 的场景也有正常使用的场景，则 test 函数就要像上面那样重复写了两次，非常糟糕。



方法② （不太推荐）：针对方法①的问题，可以采取只传函数名，不传函数的方法：

```javascript
export default {
  methods: {
    test() {
      console.log('test');
    },
    dtest: debounce('test', 1000),
  },
}
```

这样子的话，debounce 函数需作出定制化的修改：

```javascript
// 原本的 debounce 函数省略后的内容
function debounce(fn, delay) {
  ...
  return function () {
    ...
    ... setTimeout(() => {
      func.apply...;
    }, delay);
    ...
  }
}

// 改为使用函数名调用后的 debounce 函数
function vueDebounce(fnName, delay) {
  ...
  return function () {
    ...
    ... setTimeout(() => {
      this.fnName.apply...
    }, delay);
    ...
  }
}
```

可以看到，如果只是在这种情况下使用，除了传递函数名这一有点奇怪的地方外，没有太大问题。但是由于 debounce 函数定制化的修改，当在其他情况下使用时（如希望直接传函数整体的情况，像方法①的方式），可能会带来不便。通常像 debounce 函数这样的通用函数，可以随意复制到不同项目中在不同情况下使用，不希望修改成不太通用的样子。

[如何在Vue中优雅的使用防抖节流 - luckrain7 - 博客园 (cnblogs.com)](https://www.cnblogs.com/luckrain7/p/12670473.html)



方法③（推荐）：这是我自己想到的解决方式，即使用 `computed`：

```javascript
export default {
  computed: {
    dtest() {
      return debounce(this.test, 1000);
    },
  },
  methods: {
    test() {
      console.log('test');
    },
  },
}
```

于是，由于 vue 产生的问题借助 vue 解决了。
