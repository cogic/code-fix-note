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
