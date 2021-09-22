# VuePress自动部署

假设你的文章和静态文件在同一个仓库，使用 `master` 分支管理文章，使用 `gh-pages` 分支存放`VuePress`生成的静态文件。

部署`VuePress`的流程是：

- 项目打包生成静态文件：`npm run build`
- 切换打包`dist`目录下
- 上传静态文件到 `gh-pages`

在项目目录下创建一个 `deploy.sh`文件，执行该文件。省去敲代码的步骤。

```
# 确保脚本抛出遇到的错误
# 打包及切换到dist目录下
npm run docs:build && cd docs/.vuepress/dist
# 部署到github gh-pages
git init
git add -A
git commit -m 'deploy'
git push -f git@github.com:qqlcx5/qqlcx5.github.io.git gh-pages
cd -
仓库地址自行更换。
```
通过`deploy.sh`命令省去部署GitHub步骤，但是却要等待项目打包和上传等时间。

如果我把文章写好，上传到github，就可以部署`gh-pages
`岂不美哉。

GitHub官方也考虑相关的问题，然后就有了`GitHub Actions`，
GitHub Actions 是 GitHub 的持续集成服务，于2018年10月推出。

那么开始打造我们的 GitHub Actions。
## GitHub Actions

### 生成个人访问令牌token

[跳转到个人访问令牌页面](https://github.com/settings/tokens)

![-w1139](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230334.jpg)

填写 `Token` 描述，勾选 repo、write、read然后点击 `Generate token` 生成一个 `Token`

![token](media/15941321303313/15945467642127.jpg)

因为 Token 只会显示一次，所以先保存笔记本等。

![-w1030](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230337.jpg)

###  设置 仓库的Secrets

在存放博客仓库里，添加个人访问令牌。

![-w1415](media/15945439850942/15945470533312.jpg)

令牌名字取名为：ACCESS_TOKEN，复制个人访问令牌密钥进去，保存。

### 编写workflow流程

在编辑器中打开vuepress项目，在项目的根目录下创建一个`.github/workflows/main.yml文件`，内容如下：

```
name: Deploy GitHub Pages

# 触发条件：在 push 到 master 分支后
on:
  push:
    branches:
      - master

# 任务
jobs:
  build-and-deploy:
    # 服务器环境：最新版 Ubuntu
    runs-on: ubuntu-latest
    steps:
      # 拉取代码
      - name: Checkout
        uses: actions/checkout@v2
        with:
          persist-credentials: false

      # 生成静态文件
      - name: Build
        run: npm install && npm run docs:build

      # 部署到 GitHub Pages
      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@releases/v3
        with:
          ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
          BRANCH: gh-pages
          FOLDER: docs/.vuepress/dist
```

这里我写了三步：

1. 拉取代码。用到了一个 GitHub 官方 action：actions/checkout 。
1. 生成静态文件。直接运行脚本，如果你不是用的 VuePress 或者脚本不一样，要修改成你自己的。
1. 部署到 GitHub Pages。使用了第三方作者的 action：JamesIves/github-pages-deploy-action@releases/v3。我详细介绍下这个 action：

使用 `with` 参数向环境中传入了三个环境变量：

1. ACCESS_TOKEN：读取 GitHub 仓库 secrets 的 ACCESS_TOKEN 变量，也就是我们前面设置的
1. BRANCH：部署分支 gh-pages（GitHub Pages 读取的分支）
1. FOLDER：需要部署的文件在 docs/.vuepress/dist 路径，也就是我们使用 npm run docs:build 生成的静态文件的位置

> 这里有一点需要注意：我使用的是 v3 版本，需要使用 with 参数传入环境变量，且需要自行构建；网上常见的教程使用的是 v2 版本，使用 env 参数传入环境变量，不需要自行构建，可使用 BUILD_SCRIPT 环境变量传入构建脚本。

至此，配置工作均已完成。提交你的代码，就会开启自动构建。

以后，你每次有代码 push 到 master 分支时，GitHub 都会开始自动构建。

### 验证

通过`Action`可以查看项目的部署流程。

![-w1083](media/15945439850942/15945482529364.jpg)

部署失败，会接收到GitHub发送的邮件，点击图片的标题进入页面查看日志。

![-w1398](media/15945439850942/15945485535600.jpg)

正常来说没什么问题。享受 GitHub Actions 带来的愉快体验吧~

参考链接：

- [使用 GitHub Actions 自动部署博客](https://vuepress-theme-reco.recoluan.com/views/other/github-actions.html#%E8%AE%BE%E7%BD%AE-secrets)

