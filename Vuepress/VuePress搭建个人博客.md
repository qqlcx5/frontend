---
title: VuePress 搭建个人博客
date: 2018-12-28
tags:
- VuePress
---
## 项目的亮点

Vuepress诞生时候我就入手体验了，也有很多大神写过这方面的文章写的也很不错。起初并不打算想写的，想到或许可以帮助一些人更快更轻松的搭建博客。我搭建的VuePress作为模版，介绍其中一些必要的配置项，更快的了解VuePress，顺便在上面爽爽写文章文档就行。

时隔两年后，我最近公司裁员，无奈失业了，面试几家不满意就闲了下来，又回到最初的起点，总结这年学习的知识，顺便把这两年断更的文章补回来。

**本项目一些优点**

1. 自动生成侧边栏和分组
2. GitHub Actions 自动部署
3. 集合一些有趣的插件库

## VuePress

一个基于 Vue SSR 的静态站生成器，它的诞生初衷是为了支持 Vue 及其子项目的文档需求。

## 为什么不是...?

### Nuxt
VuePress 能做的事情，Nuxt 理论上确实能够胜任，但 Nuxt 是为构建应用程序而生的，而 VuePress 则专注在以内容为中心的静态网站上，同时提供了一些为技术文档定制的开箱即用的特性。

### Docsify / Docute
这两个项目同样都是基于 Vue，然而它们都是完全的运行时驱动，因此对 SEO 不够友好。如果你并不关注 SEO，同时也不想安装大量依赖，它们仍然是非常好的选择！

### Hexo
Hexo 一直驱动着 Vue 的文档 —— 事实上，在把我们的主站从 Hexo 迁移到 VuePress 之前，我们可能还有很长的路要走。Hexo 最大的问题在于他的主题系统太过于静态以及过度地依赖纯字符串，而我们十分希望能够好好地利用 Vue 来处理我们的布局和交互，同时，Hexo 的 Markdown 渲染的配置也不是最灵活的。

### GitBook
我们的子项目文档一直都在使用 GitBook。GitBook 最大的问题在于当文件很多时，每次编辑后的重新加载时间长得令人无法忍受。它的默认主题导航结构也比较有限制性，并且，主题系统也不是 Vue 驱动的。GitBook 背后的团队如今也更专注于将其打造为一个商业产品而不是开源工具。

## 快速上手

### 全局安装

在命令行或者控制台、终端执行以下操作。

```
# 创建项目目录
mkdir vuepress && cd vuepress   // 生成一个vuepress文件夹，然后进入该文件夹

# 初始化项目
npm init

# 将 VuePress 作为一个本地依赖安装
yarn add -D vuepress # 或者：npm install -D vuepress

# 新建一个 docs 文件夹
mkdir docs

# 新建一个 markdown 文件
echo '# Hello VuePress!' > docs/README.md

```

接着，在 `package.json` 里加一些脚本:

```js
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  },
```

然后就可以运行本项目

```sh
yarn docs:dev # 或者：npm run docs:dev
```

打包部署到GitHub服务器上

```sh
yarn docs:build # 或者：npm run docs:build
```

## 目录的结构

```
vuepress
├── docs
│   ├── .vuepress
│   │   ├── components (Vue 组件将会被自动注册为全局组件)
│   │   ├── public (静态资源目录)
│   │   ├── config.js (配置文件的入口文件)
│   ├── README.md
│   ├── guide
│      └── README.md
└── package.json

```

## 主题基本配置

### 配置文件

首先在你的文档目录下创建一个 `.vuepress` 目录，所有 `VuePress` 相关的文件都将会被放在这里。

一个 VuePress 网站必要的配置文件是 `.vuepress/config.js`，它应该导出一个 JavaScript 对象：
```
module.exports = {
  title: 'Hello VuePress', //网站的标题
  description: 'Just playing around' //网站的描述
}
```

### 导航栏

```
// .vuepress/config.js
module.exports = {
  themeConfig: {
    nav: [
      { text: '首页', link: '/' },
      { text: '指南', link: '/guide/' },
      { text: 'Google', link: 'https://google.com' },
    ]
  }
}
```
当你提供了一个 items 数组而不是一个单一的 link 时，它将显示为一个 下拉列表：
```
module.exports = {
  themeConfig: {
    nav: [
      {
        text: '语言',
        items: [
          { text: '中文', link: '/cn/' },
          { text: '英语', link: '/en/' }
        ]
      }
    ]
  }
}
```

## 侧边栏分组

```
// .vuepress/config.js
module.exports = {
  themeConfig: {
    sidebar: [
      {
        title: 'Group 1',   // 必要的
        path: '/foo/',      // 可选的, 标题的跳转链接，应为绝对路径且必须存在
        collapsable: false, // 可选的, 默认值是 true,
        sidebarDepth: 1,    // 可选的, 默认值是 1
        children: [
          '/'
        ]
      },
      {
        title: 'Group 2',
        children: [ /* ... */ ]
      }
    ]
  }
}
```

> 看到这里，有没有发现一件事，你写的文章可能存在`guide`目录或者其它目录下，可能几篇文章还好，但是考虑到后续有几百篇的文章，存放在侧边栏上，需要手动去配置路径，后续文章修改文件名和路径的情况下，一不下心操作失误可能博客就废的差不多的。难维护。

接下来就介绍一下，如何把文章存在目录下文章就自动生成侧边栏有没有觉得非常棒！！！

### 自动生成侧边栏

在`.vuepress` 目录下创建一个`utils`目录，然后创建`initPage.js`和`index.js` 文件

**initPage.js文件**

```
const fs = require('fs');
// 排除检查的文件
let excludes = ['.DS_Store'];

let filehelper = {
  getFileName: function(rpath,otherPath) {
    let filenames = [];
    let fileTypes = /\.md$/; //只匹配以md结尾的文件
    fs.readdirSync(rpath).forEach((file) => {
      if (excludes.indexOf(file) < 0) {
        fullpath = rpath + '/' + file;
        let fileinfo = fs.statSync(fullpath);
        if (fileinfo.isFile()) {
          if (fileTypes.test(file) > 0) {
            if (file === 'README.md') {
              file = '';
            } else {
              file = file.replace('.md', '');
            }
            file = otherPath ? otherPath + file : file
            filenames.push(file);
          }
        }
      }
    });
    filenames.sort(); // 排序
    return filenames;
  },
};
module.exports = filehelper;

```
**index.js 文件**

index.js 主要是接收参数，将参数转换成对象格式，方便在 config.js 里使用。
```
const utils = {
  genSidebar: function (title, children = [''], collapsable = true, sidebarDepth = 2) {
    let arr = new Array();
    arr.push({
      title,
      children,
      collapsable,
      sidebarDepth
    })
    return arr;
  }
};

module.exports = utils;
```

在`.vuepress` 目录下创建一个`themeConfig.js`文件

```
const path = require('path');
const rootpath = path.dirname(__dirname);
const utils = require('./utils/index');
const filehelper = require('./utils/initPage.js');

const themeConfig = {
  nav: [
    {
      text: '面试',
      link: '/Interview/',
    },
    {
      text: 'JavaScript',
      link: '/JavaScript/',
    },
    {
      text: 'Vuejs',
      link: '/vue/',
    },
    {
      text: 'CSS',
      link: '/css/',
    },
    {
      text: '其它',
      ariaLabel: 'Menu',
      items: [
        { text: 'VuePress', link: '/other/vuepress/' },
        { text: '开发工具', link: '/other/ide/' }
      ]
    },
    {
      text: '主页',
      ariaLabel: 'Menu',
      items: [
        { text: '主页一', link: '/404/' },
        { text: '主页二', link: '/404/Personal' }
      ]
    },
  ],
  
  sidebar: {
    '/JavaScript/': concatJs(),
  },
};

// JavaScript
function concatJs() {
  const arr = utils.genSidebar( 'JStst', filehelper.getFileName(rootpath + '/JavaScript/'), false);
  arr.push(...utils.genSidebar('Js-Vue',filehelper.getFileName(rootpath + '/JavaScript/vue/', 'vue/'),false));
  return arr;
}

module.exports = themeConfig;

```
通过`themeConfig.js` 把一些配置项调整放在这里，这样`.vuepress/config.js` 就可以如下代码这样写。避免文件过于太长而且代码更加优雅一些，今后一些目录调整在`themeConfig.js`稍作调整就行。

```
const themeConfig = require('./themeConfig');

module.exports = {
  title: '廖纯鑫博客',
  description: '前端、前端教程、小程序、个人博客、廖纯鑫博客',
  themeConfig
};
```

参考文献：

1. [vuepress自动生成侧边栏](https://www.jianshu.com/p/7b78f570fc4e)
2. [VuePress 中文文档](https://www.vuepress.cn/plugin/#%E6%A0%B7%E4%BE%8B)
    

## 部署
在项目目录下创建一个 `deploy.sh`文件，复制以下代码。
```
# 确保脚本抛出遇到的错误
# 上传vuepress源码及文章到码云备份
set -e
git add -A
git commit -m 'blogCode'
git push git@gitee.com:qqlcx5/vuePressCode.git master
# 打包及切换到打包后文件夹
npm run docs:build && cd docs/.vuepress/dist

# 部署到码云
git init
git add -A
git commit -m 'deploy'
git push -f git@gitee.com:qqlcx5/qqlcx5.git master

# 部署到github Page
# 删除 .git目录重新上传，目前只有想的这种方法
rm -rf .git
git init
git add -A
git commit -m 'deploy'
git push -f git@github.com:qqlcx5/qqlcx5.github.io.git master
cd -
```
仓库地址自行更换。

window用户双击`deploy.sh` 文件便会将上传文章到码云备份，及部署到GitHub Page。

```
# 在终端且进入vuepress项目中输入：

./deploy.sh

# 如果出现permission denied，就重新设置一下权限再运行，

chmod 777 deploy.sh

./deploy.sh
```

## 后续

我写VuePress教程初衷就是对博客一些文章作一些更好的管理，易维护且有好展示给用户看。花里胡哨的东西，我觉得违背的简约美观的风格，最关键的是我也不会弄，这才是关键。后续我会加入博客炫酷元素，如果我有机会的话，一起爬山吗？

## 订阅

![WeChat](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.1/img/WeChat.png)

[个人博客](https://qqlcx5.github.io/)