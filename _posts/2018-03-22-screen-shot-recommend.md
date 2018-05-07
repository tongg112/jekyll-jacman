---
layout: post
title:  "Ubuntu17.10下截图工具分享"
date:   2018-03-22 21:18:09 +0800
---

Ubuntu下的截图软件之前在14.04和16.04版本使用shutter是非常顺手的。
编辑热键使用ctrl+alt+A快速截图，还可以标注图片，然后粘贴到微信等聊天窗口。
但是在Ubuntu的17.10版本，Display Server使用Wayland而不是用X.Org的情况下，shutter是无法使用的。
使用Wayland代替X.Org，截图软件也就需要换了，系统自带的截图工具也能用，但是功能太单一。
于是在Google上发现了flameshot，真的很好用。

![](https://github.com/lupoDharkael/flameshot/raw/master/img/appPreview/animatedUsage.gif)

安装与配置可以参考GitHub仓库中的说明文档。

Ubuntu18.04可以支持apt安装了。（2018年05月07日更新）

> sudo apt-get install flameshot

参考链接：https://github.com/lupoDharkael/flameshot
