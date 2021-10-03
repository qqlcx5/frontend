---
title: github文件转jsdelivr地址
date: 2021-7-28
tags:
- Github
---
## sessionStorage

两种引用方式：

- 存入仓库分支里面，直接引用
- 创建版本号后，在引用

在这是不是很好奇，为什么引用要弄成这么麻烦。网上我看过类似教程，他们总是忽略一个关键的原因避而不谈，因为这点也原因差点坑了我。超过50M `jsdelivr`就无法访问。为了能免费撸羊毛，建议使用发布版本号方案，只要单次版本号的大小不超过50M即可，多次版本号就可以突破50M限制。

> 程序包大小超出了配置的50 MB限制。

很好奇为什么我会发现这个问题。我作了一个假设，如果仓库存放超多的图片的资源，且能够访问的话，那么这种教程，作为一种免费撸羊毛的手段在中国应该被大佬推荐，不至于到现在还不温不火。终其原因我尝试上传几张超大的图片，就被提示无法访问，分成两次版本号后（单次不超过50M）总大小超过50M却能正常访问。

**1. 直接引用**

格式为：

```
https://cdn.jsdelivr.net/gh/<用户名>/<仓库名>/<文件及路径>
```
例：
```
GitHub

https://github.com/qqlcx5/figure-bed/blob/master/img/20200710230327.jpg

转成 jsdelivr

https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed/img/20200710230327.jpg
```
总结：

```
github.com`替换成cdn.jsdelivr.net/gh

/blob/master 删除
```

**版本号**

版本号用@符链接。格式：

```
https://cdn.jsdelivr.net/gh/<用户名>/<仓库名>@[版本号]/<文件及路径>
```

例：
```
GitHub

https://github.com/qqlcx5/figure-bed/blob/1.0/img/20200710230327.jpg

转成 jsdelivr

https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230327.jpg
```
总结：

```
github.com`替换成cdn.jsdelivr.net/gh

/blob/ 替换成 @ 
注：1.0 创建的版本号
```

已发布的版本不会受到仓库内容变化的影响
如何按版本号引用。链接相对稳定也是我特别推荐的原因。
