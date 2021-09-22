# GitHub + jsDelivr + PicGo 打造稳定快速、高效免费图床

![git](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230327.jpg)

## 目前图床优劣

1. 微博等博客图床：以前用的人比较多，从 2019 年 4 月开始开启了防盗链，凉凉。
2. SM.MS：运营四年多了，也变得越来越慢了，到了晚上直接打不开图片，速度堪忧。
3. 其他小众图床：随时有挂掉的风险。
4. Imgur等国外图床：国内访问速度太慢，随时有被墙的风险。
5. 优质储存服务：例如七牛云、又拍云、腾讯云COS、阿里云OSS等，操作繁琐，又是实名认证又是域名备案的等。说真的以后网站的访问量大的话，还的买带宽等服务，按月收费，费用高。
6. GitHub 图床：免费，但是国内访问速度慢（利用 jsDelivr 提供的免费的 CDN 加速，鸡贼）

## 搭建教程

1. 新建 GitHub 仓库
2. 创建个人访问令牌
3. 配置 PicGo 设置
4. 上传图片，获取jsDelivr 地址

### 新建 GitHub 仓库

创建一个仓库，用来存放图片。

![-w1059](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230340.jpg)

### 生成个人访问令牌Token

[前往设置](https://github.com/settings/tokens)

作用：授权仓库的操作权限，通过API实现自动化。

![-w1139](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230334.jpg)

然后填写 `Token` 描述，勾选 repo、write、read然后点击 `Generate token` 生成一个 `Token`

![token](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200714002302.jpg)


因为 Token 只会显示一次，所以先保存笔记本等。

![-w1030](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230337.jpg)

### 配置 PicGo
[前往PicGo官网](https://github.com/Molunerfinn/picgo/releases)

安装好后开始配置图床

1. 设定仓库名：按照 用户名/仓库名 的格式填写（就是刚才创建的仓库）

2. 设定分支名：master

3. 设定 Token：粘贴之前叫你保存的Token。

1. 设定自定义域名：它的的作用是，在图片上传后，PicGo 会按照自定义域名+上传的图片名的方式生成访问链接，放到粘贴板上。
`https://cdn.jsdelivr.net/gh/用户名/仓库名`

![-w800](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230329.jpg)

### 上传图片测试
上传图片后，在相册可以看到刚才的图片，点击复制的URL就是通过`jsdelivr` CDN加速的。

![-w800](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200710230348.jpg)

此外 PicGo 还有相册功能，可以对已上传的图片进行删除，修改链接等快捷操作，PicGo 还可以生成不同格式的链接、支持批量上传、快捷键上传、自定义链接格式、上传前重命名等，更多功能自己去探索吧！

最后，以上步骤只是通过工具提升效率罢了，了解github地址转化成jsDelivr地址规则，其实就能愉快的撸羊毛。

> 某分支和版本号大小不能超过50M会直接被jsDelivr屏蔽。所以单某个分支接近临界值，换分支继续就得了。


相关文章

[GitHub 通过 jsdelivr CDN加持](https://qqlcx5.github.io/other/github/)


参考文章
- [https://www.cnblogs.com/sitoi/p/11848816.html](https://www.cnblogs.com/sitoi/p/11848816.html)