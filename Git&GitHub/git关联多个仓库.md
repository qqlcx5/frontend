# git关联多个仓库

例如我有下面两个仓库：

```js
https://gitee.com/qqlcx5/frontend.git
https://github.com/qqlcx5/frontend.git
```
**方法一 命令操作**
先添加第一个仓库：
git remote add origin https://gitee.com/qqlcx5/frontend.git

再添加第二个仓库：
git remote set-url --add origin https://github.com/qqlcx5/frontend.git

如果还有其他需要关联的仓库，同上操作。

然后使用下面命令提交：

```sh
git push origin --all
```

**方法二 修改文件**

每个项目都有一个`.git`文件夹的隐藏文件，可以通过编辑器打开`.git/config`文件

可以看到这样的配置：

```js
[remote "origin"]
url = https://gitee.com/qqlcx5/frontend.git
fetch = +refs/heads/*:refs/remotes/origin/*
url = https://github.com/qqlcx5/frontend.git
```
