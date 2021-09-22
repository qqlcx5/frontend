# Mac终端替代工具iTerm2

## 什么是iTerm2？

iTerm2是Terminal的替代品，是iTerm的后继产品。它适用于MacOS 10.12或更高版本的Mac。iTerm2将终端带入了您从未想过一直想要的功能，使其进入了现代时代。

**效果**

![cloud](https://cloud.githubusercontent.com/assets/2618447/6316862/70f58fb6-ba03-11e4-82c9-c083bf9a6574.png)

### 一、item2软件下载

[item2官网](https://www.iterm2.com/)

 我上传到蓝奏云，下载速度还不错。[前往蓝奏云下载](https://qqlcx5.lanzous.com/ibyghcf)

> 如果下载以下资源下不下来，可以尝试科学上网配`Proxifier`就可以走VPS通道。

### 配置 Oh My Zsh

[Oh My Zsh](http://ohmyz.sh/) 是一款社区驱动的命令行工具，正如它的主页上说的，Oh My Zsh 是一种生活方式。它基于zsh命令行，提供了主题配置，插件机制，已经内置的便捷操作。给我们一种全新的方式使用命令行。

**安装**

1. 官网推荐安装方式：

```
第一种 curl:

$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

第二种 wget:

$ sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

安装好之后，需要把 Zsh 设置为当前用户的默认 Shell

```
$ chsh -s /bin/zsh
```

然后，将主题修改为ZSH_THEME="agnoster"

```
$ vim ~/.zshrc
输入i进入编辑模式,将ZSH_THEME=""编辑为ZSH_THEME="agnoster"
按下esc键,退出编辑,
:wq保存退出
修改成功!
```
![agnoster](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.2/img/20200725002842.jpg)

上面效果主题为`agnoster`，不喜欢可以选择其他主题 [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)

### 配置 Meslo 字体

1. 字体文件下载 [蓝奏云](https://qqlcx5.lanzous.com/ibzub5c)

2. 然后打开安装后，选中item2软件，按`Command + ,`键，打开 `Preferences` 配置界面，然后`Profiles -> Text -> Font -> Chanage Font`，选择 `Meslo LG M Regular for Powerline` 字体。
3. 小提示，字体必须安装，不然一些特殊字符会出现乱码

如图所示

![-w930](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.2/img/20200725002848.jpg)

### iTerm2 快捷命令

```
command + t 新建标签
command + w 关闭标签
command + 数字 command + 左右方向键    切换标签
command + enter 切换全屏
command + f 查找
command + d 水平分屏
command + shift + d 垂直分屏
command + option + 方向键 command + [ 或 command + ]    切换屏幕
command + ; 查看历史命令
command + shift + h 查看剪贴板历史
ctrl + u    清除当前行
ctrl + l    清屏
ctrl + a    到行首
ctrl + e    到行尾
ctrl + f/b  前进后退
ctrl + p    上一条命令
ctrl + r    搜索命令历史
```

## 以下按需加载

### 配置 iTerm2 主题

选中item2软件，按`Command + ,`键，打开 `Preferences` 配置界面，然后`Profiles -> Colors` 选择 `Solarized Dark` 主题。

按图中所示操作

![-w929](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.2/img/20200725002854.jpg)

## 推荐插件

### zsh-autosuggestions

它是Oh-myszh的一个插件，作用基本上是根据历史输入指令的记录即时的提示，能够很大的提高效率。

1.克隆到插件目录:

```
git clone git://github.com/zsh-users/zsh-autosuggestions
```

2.vim ~/.zshrc

```
plugins=(git zsh-autosuggestions)
```

![-w482](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.2/img/20200725002851.jpg)


### zsh-syntax-highlighting

这是一个命令高亮插件，输入为绿色时表示可用命令，路径带有下划线时表示可用路径

1.克隆到插件目录：

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```
2.复制到终端回车键

```
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
```
3.配置生效

```
source .zshrc
```

### iTerm2 隐藏用户名和主机名

1. vim ~/.zshrc
2. 在底部新增 DEFAULT_USER="电脑用户名"

不知道自己电脑用户名的,在终端输入whoami，输出用户名

```
$ whoami
```

## 订阅

![WeChat](https://cdn.jsdelivr.net/gh/qqlcx5/figure-bed@1.1/img/WeChat.png)



