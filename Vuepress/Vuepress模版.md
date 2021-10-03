---
title: Vuepress 模版
date: 2018-12-28
tags:
- VuePress
---
## 目录
- [安装环境](#安装环境)
- [目录结构](#目录结构)
- [运行项目](#运行项目)

[vuepress-template](https://qqlcx5.github.io/vuepress-template/) 是一个为编写技术文档而诞生的，它基于[vuePress](https://vuepress.vuejs.org/zh/)实现，内置自动生成侧边栏，动漫模型、自动打包部署GitHub Actions、Gitee Page同步更新（原Plus会员的功能）、还有听歌组件等。

快速的搭建属于自己的静态页面，只要转移自己的文章目录和配置导航栏页面就行。

## 安装环境
你需要在本地安装 `node`和`- [vuepress-template](#vuepress-template)

`，如果你本机没有`node` 点击下面地址跳转到官网下载安装。

* [node 官网](https://nodejs.org/en/)
* [git 官网](https://git-scm.com/downloads)

## 目录结构

```.
├── docs
│   ├── .vuepress
│   │   ├── components
│   │   ├── config
│   │   │   ├── theme.js (导航和侧边栏)
│   │   │   └── plugins.js (插件的)
│   │   ├── theme
│   │   │   └── Layout.vue
│   │   ├── public
│   │   ├── styles
│   │   │   ├── index.styl
│   │   │   └── palette.styl
│   │   ├── config.js (可选的)
│   │
│   ├── README.md
│   ├── 指南
│   │   └── 介绍.md
│   └── config.md
│
└── package.json
```

## 运行项目
```bash
# 克隆项目
git clone https://github.com/qqlcx5/vuepress-template.git

# 进入项目目录
cd vuepress-template

# 安装依赖
npm install

# npm 下载速度慢 可以指定 registry 来解决 npm 安装速度慢的问题。
npm install --registry=https://registry.npm.taobao.org

# 本地开发 启动项目
npm run docs:dev
```
启动完成后打开浏览器访问 [http://localhost:8080](http://localhost:8080/vuepress-template/)， 你看到下面的页面就代表操作成功了。


