---
title: MacOS Catalina 已损坏无法打开
date: 2018-12-28
tags:
- Mac
---
**macOS Catalina 10.15 版本**

### 软件已损坏无法打开

1. 打开终端；
2. 输入以下命令，回车；`
sudo xattr -d com.apple.quarantine /Applications/xxxx.app`注意：/Applications/xxxx.app 换成你的App路径，或在输入 `sudo xattr -d com.apple.quarantine` 后将软件拖进去，然后再回车。

如果提示”No such file”为文件路径有误，重命名一下软件名称，再重试即可。
3. 重启App即可。

**10.15以下版本**

**任何来源**
1. 打开终端；
2. 输入 `sudo spctl –master-disable` 
3. 重新打开「系统偏好设置-安全与隐私-通用」即可看到「任何来源」选项。

不知道自己系统版本的，点击左上角小苹果=>关于本机
![-w847](http://qqlcx5.oss-cn-shanghai.aliyuncs.com/mweb/20210923-15878922092293.jpg)


