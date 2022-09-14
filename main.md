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
