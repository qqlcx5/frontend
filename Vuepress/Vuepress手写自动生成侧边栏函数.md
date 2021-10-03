---
title: Vuepress 手写自动生成侧边栏函数
date: 2018-12-28
tags:
- VuePress
---
## 前言

如果你在工作中遇到问题、在面试中遇到疑惑、在前端路上遇到了阻碍，都可以加入我们前端有道 Family，我会竭尽全力为大家答疑解惑，让我们共同努力，一同成长。

点击添加技术交流群或者关注公众号🎨[前端有道](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@v1.0.0/image/pubic_w.jpg)

主要是配置侧边栏过于繁琐，文件目录结构调整或者文件名修改，需要重新修改文件路径，实在麻烦的很，我又不想在上面花费太多时间，毕竟有时间心血来潮觉得在某目录下比较合适，有得全局替换路径，可能不注意就修改错了，上传到服务器上，又是一波折腾，哈哈听你一本正经的说，就是太懒了，瞎说什么大实话，哈哈。

### Usage

通常文章的目录分为两种，单目录结构，和多目录结构。
下面简单阐述的，如何使用工具函数，生成目录。

感兴趣同学，又不想手动配置文件繁琐，可以运行配置好的项目，运行或者用来调试学习。点击跳转[GitHub仓库](https://github.com/qqlcx5/vuepress-template.git)，不懂地方可以微信问我

**单目录结构，如图所示**

![](media/16322124391604/16322141978221.jpg)

**配置sidebar**
在`config.js` 中配置`sidebar`项目
![](media/16322124391604/16322147648791.jpg)

**效果**
![](media/16322124391604/16322153313749.jpg)

**tips**

- 文件名切记注意大小写，别问为啥，被坑过
- 工具函数和文章路径记得对应上，不然会报错

如果一直报错或者查看生成数据目录，可以开启调试模式，在`package.josn`运行命令后缀添加`--debug`

在`console.log(vue)`，可以在终端中查看打印的数据，如图所示
![](media/16322124391604/16322157581113.jpg)

**多目录**

![](media/16322124391604/16322160639825.jpg)

**配置sidebar**
![](media/16322124391604/16322162340880.jpg)

**效果如图**

![](media/16322124391604/16322163532504.jpg)

**文章排序**
我是有追求的人，造出来数据目录要按照我想要的顺序来排序，不然生成目录瞎放影响我博客的美观，强迫症患者，耶安排。

在目录前缀添加序号不就得了，我开始也是这么想的，当我写的文章超过十篇以上，发现怎么`10.CSS.md`排在`01.CSS.md`的后面，不应该在最后面吗？查看数据发现，读取的文件不是按照目录顺序排序的，竟然发现这个问题就解决这个问题。

给数据进行sort排序一下，好了顺序终于对了，巴适得很。
![](media/16322124391604/16322183736161.jpg)

可以按照这样的命名来定义文件规范，后面调整比较灵活点。

- 文件夹可以取`10，20，30`顺序开头，假如后面如果想在`10~20`之间插入新的目录，这样只要写`11，12`就行，
- 文件可以按照这样来命名，如果考虑到后面文件有100多篇，可以采用`100，200，300`开头，这样可以在中间插入更多文件而又不用重新编辑后面的文件名。

### 深入了解

首先我们了解我们要什么样的数据结构。

**目录**
![](media/16322124391604/16322192261139.jpg)

我们要得到下面这样的数据。

```js
  sidebar: {
    '/frontend/': [
      '', // README可以不用写文件名
      {
        title: 'HTML',
        collapsable: false,
        children: [
          ['HTML/HTML快速入门', 'HTML快速入门'],
          ['HTML/HTML基础知识', 'HTML基础知识'],
        ],
      },
      {
        title: 'CSS',
        collapsable: false,
        children: [
          ['CSS/01.CSS基础', 'CSS基础'],
          ['CSS/02.CSS美化', 'CSS美化'],
        ],
      },
    ],
  },
```

那么我们当然的通过node中`fs.readdirSync`API来读取文件，得到一个数组，数组中的数据如图下
![](media/16322124391604/16322196329424.jpg)

这样的数据肯定不能直接使用，那么我们可以将每个字符串分割成数组，得到`["Vue Popover组件开发","md"]`
由于考虑到文件可能有序列号，不想在目录中展示那么我们通过`[文件路径， 文件名]`格式来重置，又考虑到`README`文件比较特殊，不能写成`['README', 'README']`形式，所以遇到`README`时，写成`''`推到数组第一项为主，最终的数据
```
[
  {
    title: 'Vue.js',
    collapsable: false,
    children: [
      '',      
      [Array], [Array]
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array] 
    ]
  }
]
```
如果我们想通过一个父文件夹来包含多个子文件夹的话，显然上面的方法不适用这种情况，我们要得到数据应该一个文件夹一个归类，如下面这种数据
```
[
  {
    title: 'Vue.js',
    collapsable: false,
    children: [
      '',      
      [Array], [Array]
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array] 
    ]
  },
  {
    title: 'Vue3.js',
    collapsable: false,
    children: [
      '',      
      [Array], [Array]
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array], [Array],
      [Array] 
    ]
  }
]
```
所以当我们通过`stat.isDirectory()`方法来读取的文件是文件夹情况，那我们就定义一个对象，里面`children`属性是一个空数组，然后通过递归的方法重新调用该方法将读取到文件push到`children`数组里面。哈哈大功告成。

```js
const fs = require('fs') // 文件模块
const path = require('path') // 路径模块
const docsRoot = path.join(__dirname, '..', '..', '..') // docs文件路径
const chalk = require('chalk') // 命令行打印美化
const log = console.log

function ReadFile(dir = docsRoot, filesList = [], fpath = '') {
  let files = fs.readdirSync(dir)
  // 10 1排序错误
  if(Array.isArray(files)){
    files.sort((item1, item2) => {
      let c1 = item1.split('.')[0]
      let c2 = item2.split('.')[0]
      return c1 - c2
    })
  }
  console.log('------');
  console.log(files);
  files.forEach((item, index) => {
    let filePath = path.join(dir, item)
    const stat = fs.statSync(filePath)
    // log('isDirectory-------------------', stat.isDirectory(), item)
    // isDirectory 返回是否文件夹, 4.file.md dir:/Users/xx/reg-rules-js-site/docs/regular
    const fileNameArr = path.basename(filePath).split('.')
    if (stat.isDirectory() && item !== '.vuepress') {
      // 生成目录名
      let title = fileNameArr.length > 1 ? fileNameArr[1] : fileNameArr[0]
      if(!title){
        log(chalk.yellow(`warning: 该文件夹 "${filePath}" 没有按照约定命名，将忽略生成相应数据。`))
        return
      }
      filesList.push({
        title,
        collapsable: false,
        children: [],
      })
      // log('递归读取文件夹的文件', path.join(dir, item), filesList[index].children, item)
      // 递归读取文件夹的文件 /Users/another/Documents/self-study/reg-rules-js-site/docs/test/test2 [] test2
      ReadFile(path.join(dir, item), filesList[index].children, item)
    } else {
      // 生成文件名数组
      let name = null
      title = null
      typeFile = null
      pathName = null
      let cloneArr = [...fileNameArr]
      typeFile = cloneArr[cloneArr.length - 1]
      if (fileNameArr.length > 1) {
        cloneArr.pop()
        name = cloneArr.join('.')
        pathName = fpath ? `${fpath}/${name}` : name
        title = cloneArr.length > 1 ? cloneArr[1] : cloneArr[0]
      } else {
        log(chalk.yellow(`warning: 该文件 "${filePath}" 没有按照约定命名，将忽略生成相应数据。`))
        return
      }

      log('name', name, pathName, typeFile, title)
      // 过滤非md文件
      if (typeFile === 'md') {
        if(name === 'README') return filesList.unshift('')
        filesList.push([pathName, title])
      }
    }
  })
  return filesList
}

module.exports = ReadFile
```
最开始本来是一个简单的功能，用来处理单目录结构，后面想着单目录如果多的情况下，`nav`肯定不放下，要么肯定不好看，后解决多目录文件，线上一看，有的文件名长短不一样，越看越觉得变扭，然后就重新写，架构新数据格式，嗯，现在看好多了。