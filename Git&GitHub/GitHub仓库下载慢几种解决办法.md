---
title: GitHub仓库下载慢几种解决办法
date: 2021-7-28
tags:
- Github
---
**目录**

1. vps/代理
2. 码云中转下载
3. cnpmjs镜像
4. hosts
5. GitClone 缓存加速(新)

## 科学的上网

全局上网后如果下载速度并没有显著提升的话，搭配代理`Proxifier`等工具。


## 码云
登录码云官网后点击 `+` 选择从GitHub导入仓库。

![-w477](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200712161533.jpg)

然后进入这个页面。

![-w750](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200712161535.jpg)

导入成功，复制url或者地址，用法和github一样。

![-w1052](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.0/img/20200712161542.jpg)

## cnpmjs 镜像

这个方法更加的简单,只需要修改你的路径`github.com`为`github.com.cnpmjs.org`

```
git clone https://github.com/qqlcx5/editor.git

#改为
git clone https://github.com.cnpmjs.org/qqlcx5/editor.git

```

## hosts

**解决办法**

直接找出github域名所对应的IP地址，直接添加在本地host中。这样每次请求gihub时就无须在向DNS查询地址了。

**DNS查找IP地址**

将下列的IP地址替换成点击对应的地址获取IP地址。

- [github.global.ssl.fastly.net](https://fastly.net.ipaddress.com/github.global.ssl.fastly.net)

- [github.com](https://github.com.ipaddress.com/)

- [github-cloud.s3.amazonaws.com](https://amazonaws.com.ipaddress.com/github-cloud.s3.amazonaws.com)

例：

```
199.232.69.194 github.global.ssl.fastly.net 
140.82.112.3 github.com
52.217.82.236 github-cloud.s3.amazonaws.com
```

### window

在我的电脑复制下列地址回车键，看到`hosts`文件，然后复制我的桌面。

```
C:\Windows\System32\drivers\etc
```
将刚才的地址复制进去保存，然后将保存的文件拖拽到刚才的地址进行替换。（需要管理权限）

### MAC

按快捷键 `shift` + `option` + `G`弹出对话框，粘贴 `/etc/hosts`，回车键，看到host复一份修改，然后替换原来文件，类似上面window操作。

### 通过工具快捷修改hosts

SwitchHosts开源地址: [https://github.com/oldj/SwitchHosts/releases](https://github.com/oldj/SwitchHosts/releases)

MacOS下载懒人链接: 
[SwitchHosts._macOS_3.5.4.5517.dmg](
https://github.com/oldj/SwitchHosts/releases/download/v3.5.4/SwitchHosts._macOS_3.5.4.5517.dmg)

Windows下载懒人链接: [SwitchHosts._windows_portable_3.5.4.5517.exe](https://github.com/oldj/SwitchHosts/releases/download/v3.5.4/SwitchHosts._windows_portable_3.5.4.5517.exe)

### 刷新 DNS 缓存
在终端或CMD中，执行以下命令：

```
Window： ipconfig/flushdns
mac执行：sudo dscacheutil –flushcache
```

## GitClone

当开发者经gitclone.com中转clone github上的代码库时，gitclone.com会对代码库进行mirror缓存，以后有开发者clone时，将直接从mirror缓存中获取数据，mirror将在每天夜间从github.com同步。
​ 
### 设置git的超时参数
​ 
​ 首先要设置git的超时参数，防止项目较大时，服务器端mirror时间过长导致git报504（超时）
​ 
```
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime 999999 
```

### 方法1：修改URL

该仓库总大小30M左右，拿来作为实验对象。
```
github
https://github.com/qqlcx5/editor

GitClone
git clone https://gitclone.com/github.com/qqlcx5/editor
```
大概在10秒内下载完成，觉得不错。

### 方法二 修改git配置
```
git config --global url."https://gitclone.com/github.com/".insteadOf https://github.com/
```
在这补充一点，官网推荐 `git config --global url.“https://gitclone.com/".insteadOf https://`，个人觉得不是友好，毕竟还有很多仓库，如果上面这样写的话，估计也会走`gitclone`通道。估计引发XX问题。

了解更多[前往gitclone官网](https://gitclone.com/)

## 订阅

![WeChat](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.1/img/WeChat.png)

参考链接

[https://www.jianshu.com/p/0493dcc15d6f](https://www.jianshu.com/p/0493dcc15d6f)
