# 常用的npm&yarn操作命令都在这了

如果你在工作中遇到问题、在面试中遇到疑惑、在前端路上遇到了阻碍，都可以加入我们前端有道 Family，我会竭尽全力为大家答疑解惑，让我们共同努力，一同成长。

点击添加技术交流群或者关注公众号🎨[前端有道](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@v1.0.0/image/pubic_w.jpg)

有时候想查个命令，或者换个镜像找了几篇文章才找到，最近闲着没事干，干脆整理一篇文档，以后就不用在网上瞎搜有的还写不全。

## Usage

### 全局安装

[安装node](http://nodejs.cn/download/)

mac同学，安装全局包会提示权限不足，在前面添加`sudo`就行

```sh
// 安装node自带npm，检查是否安装成功，执行如下命令
npm -v  
// 6.14.15

// yarn
npm install -g yarn

// cnpm 
npm install -g cnpm

// pnpm
npm install -g pnpm
```
cnpm、pnpm 用法类似npm，yarn不一样地方列出来单独说明。

### 初始化一个项目
```sh
npm init
// OR
yarn init

// 快速生成的package.json默认配置
npm init -y
yarn init -y
```

### 安装项目依赖
**npm / cnpm / pnpm**

```
npm install [package]
// 简写
npm i [package]

// 安装指定版本
npm i[package]@[version]
npm i [package]@[tag]

// 全局安装依赖
npm i -g [package]
```
**yarn**
```sh
yarn add [package]

// 安装指定版本
yarn add [package]@[version]
yarn add [package]@[tag]

// 全局安装依赖
yarn global add [package]
```

**小栗子**

```
// 安装指定版本jquery
npm i jquery@3.0.0

// 安装最新版本
npm i react@latest

// 安装的依赖项都可以通过package.json 查看
"dependencies": {
    "@loadable/component": "^5.14.1",
    "prismjs": "^1.23.0",
    "react-dom": "^17.0.2",
    "react-helmet": "^6.1.0",
    "react-router-dom": "^5.2.0",
    "jquery": "^3.0.0",
    "react": "^17.0.2",
},
```

**Tips1 意外情况**

可能安装依赖卡断，或者中断安装依赖，后面在重新安装，会提示报错这是因为有缓存的原因。

方式一

删除`node_modules`包重新安装。

方式二

删除缓存
```
npm cache clean --force
```

**Tips2 --save--dev作用**

在安装我们依赖的时候，经常会看到后缀 `--save`或`--save--dev`，入行那会什么都不懂照着写去，能用就行，结果开发好好的，线上就是不行。
```sh
npm install vue --no-save
npm install vue --save
npm install @babel/core --save-dev
```

**`--no-save`**
查看package.json，文件内容不发生改变，在运行项目时能正常运行，当npm i时候，不会安装该依赖，提示安装该依赖。

**`--save`**
查看package.json 会有一个dependencies对象，里面就是项目运行需要的依赖。
dependencies 代表项目运行所依赖的模块
简写 -S
```
npm install express -S
```

**`--save-dev`**
查看package.json 会有一个devDependencies对象，里面就是项目开发时候需要的依赖。
devDependencies 代表项目开发所需要的模块
如：babel 是发布时，将 ES6 代码编译成 ES5 ，那么 babel 就是devDependencies。
简写 -D
```
npm install express -D
```


**Tips3 ^和~的区别**
```
// package.json中^和~的区别

"dependencies": {
    "vue": "~2.5.0",
    "es6-promise": "^2.0.0"
},

~符号
假设Vue后面更新新的版本，已经到2.6.0或者3.2.0以上
当我们重新安装项目依赖，只会匹配到2.5.x的最新版本，不会安装到2.6.0及以上

^符号
假设es6-promise更新3.0.0，当我们重新安装项目依赖，es6-promise始终是2.0.0
```

**Tips4 版本号代表含义**

```
"dependencies": {
    "vue": "2.6.14"
}
```
第一位表示：主版本号，常用于大版本更新，可能不兼容旧版本。

    如Vue2.0和Vue3.0,虽然说向下兼容，但是部分语法存在兼容问题。

第二位表示：次版本号，增加了新的功能，基本向下兼容。

第三位表示：补丁号， 修复了bug等。

### 管理全局包

**查看 npm 全局安装过的包**
```
npm list -g --depth=0
```
![](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210920-16321258905650.jpg)

**查看全局包需要更新**

```
npm outdated -g --depth=0
```

![](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210920-16321260754306.jpg)

**更新全局的依赖**
```
npm update -g

// 更新指定依赖
npm i [package]

// 可能报错 Remove the existing file and try again, 
or run npm with --force to overwrite files recklessly.

1. 卸载依赖
npm uninstall -g [package]

清理 npm 缓存
npm cache clean --force

重新安装
npm i -g [package] 

// 第二种 强制安装
npm install -g [package] --force 
// 也适用于yarn
```

**查看 yarn 全局安装过的包**

```
yarn global list --depth=0
```

![](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210920-16321267733120.jpg)

**查看全局包需要更新**
没有，网上找了很久没有相关文章。

**更新全局的依赖**
```
yarn global upgrade

// 更新指定依赖
yarn global add <package>@lates
yarn global add yrm@lates
```
### 升级依赖项

**npm/yarn查看项目中包是否更新**

```
yarn outdated
```
![](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210920-16321271144870.jpg)

```
"dependencies": {
    "vue": "~2.6.0"
},

npm update vue
// 不会更新package.json文件
npm i vue   // 重新安装，会更新最新版本

yarn upgrade vue@2.6.14 指定版本
yarn upgrade vue@^ //选择指定版本

"dependencies": {
    "vue": "^2.6.14"
}
```

### 删除依赖包
```
"dependencies": {
    "vue": "^2.6.14"
}

npm uninstall vue

yarn remove vue

// 删除全局依赖
npm uninstall -g <package>
yarn global remove <package>
```

### 更新项目中所有依赖项

* npm-check
* npm-check-updates

二者目的相同，只是在更新过程中的一些交互展示形式存在一定的差异

#### npm-check-updates

**安装**
```
npm install -g npm-check-updates
```
**使用**
```
// 查看可更新包
ncu

[====================] 12/12 100%
 @vssue/api-github-v4                         ^1.4.0  →   ^1.4.7     
 @vssue/vuepress-plugin-vssue                 ^1.4.6  →   ^1.4.8     
 @vuepress-reco/vuepress-plugin-back-to-top   ^1.5.7  →   ^1.6.0     
 @vuepress/plugin-google-analytics            ^1.8.1  →   ^1.8.2     
 @vuepress/plugin-pwa                         ^1.8.1  →   ^1.8.2     
 dayjs                                       ^1.10.4  →  ^1.10.7     
 vuepress                                     ^1.8.1  →   ^1.8.2     
 vuepress-plugin-live2d-model                 ^1.0.0  →   ^1.0.7     
 vuepress-plugin-one-click-copy               ^1.0.2  →   ^1.0.6 
 
 // 升级所有依赖项
 ncu -u
 ncu -u vuepress dayjs ...
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6c51d15b444043379048fb25b1e51906~tplv-k3u1fbpfcp-zoom-1.image)


#### npm-check

**安装**
```
npm install npm-check -g
```
**使用**
```
// 查看可更新包
npm-check
 
// 升级所有依赖项
npm-check -u
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e97e639eb084478bb5500169b0a74f3~tplv-k3u1fbpfcp-zoom-1.image)

### 切换国内镜像

**目前常用的镜像列表**

```
  npm ---- https://registry.npmjs.org/
  cnpm --- https://registry.nlark.com/
  taobao - https://registry.npm.taobao.org
  yarn --- https://registry.yarnpkg.com/
  tencent- https://mirrors.cloud.tencent.com/npm/  
```

**查看yarn当前镜像源**

```sh
yarn config get registry

npm config get registry

cnpm config get registry

pnpm config get registry

//https://registry.nlark.com/
```
**设置镜像源**
```
// 全局使用
yarn config set registry https://registry.npm.taobao.org

npm config set registry https://registry.nlark.com/
// ...

// 临时在项目中使用
npm install --registry https://registry.npm.taobao.org
```

**还原镜像源**

```
npm config set registry https://registry.npmjs.org
// 根据上面的镜像列表替换就行。
```

### nrm / yrm 管理镜像源工具

`yrm/nrm` 不仅可以快速切换镜像源，还可以测试自己网络访问不同源的速度，且`yrm/nrm`用法都相同。

**安装 yrm /nrm**
```
npm install -g yrm
// or
npm install -g nrm
```
**列出当前镜像源列表**
```
yrm ls

npm -----  https://registry.npmjs.org/
cnpm ----  http://r.cnpmjs.org/
taobao --  https://registry.npm.taobao.org/
nj ------  https://registry.nodejitsu.com/
rednpm -- http://registry.mirror.cqupt.edu.cn
skimdb -- https://skimdb.npmjs.com/registry
yarn ----  https://registry.yarnpkg.com
```
**使用，测试**
```
yrm use taobao
// https://registry.npm.taobao.org/

yrm test taobao
// taobao - 187ms
```

### 建议

**Windows用户**

推荐使用`yarn/npm`，可能`cnpm/pnpm`安装速度优于`yarn/npm`，但是可能造成诡异的 bug，比如项目运行不起来等等，最简单直接的方法就是删除`node_modules`重新安装。

如图所示，图中的项目通过`pnpm/cnpm`安装的依赖项，
直接运行不起来，之所以只针对`Win`系统，因为`Win`系统删除`node_modules`快则几分钟，慢则十几分钟，而`mac`直接秒删，试错成本低可以尝试。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/212bea2dd98847e9a98573917091cf23~tplv-k3u1fbpfcp-zoom-1.image)

Q: 项目通过`cnpm/pnpm` 安装依赖怎么知道会不会有诡异的 bug。

A: 项目如果能运行起来，99%不会有诡异的 bug，万一调试中报错，写的代码有没有问题，可以查看报错日志自行查看一下相关教程。



### 参考文章

* https://blog.csdn.net/xuaner8786/article/details/81630445
* https://juejin.cn/post/6844904114762022926
* https://www.npmjs.com/package/npm-check-updates
* https://juejin.cn/post/6913833065647341581
* https://github.com/dylang/npm-check