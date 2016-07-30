title: 安装 hexo 及 hexo-math
date: 2016-07-31 00:16:33
tags: hexo
---
其实我的需求只是 hexo 加上一个支持 mathjax 的插件, 最后选择了 hexo-math.
不过 hexo-math 不支持最新的 hexo 3.2.2 版本. 所以现在不得不选择之前的版本. 现在完整记录一下安装的过程
## hexo 安装
``` bash
# 首先安装 nvm 及 node.js
$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh
$ nvm install 4

# 安装 hexo-cli, 由于最新版和 hexo-math 不兼容, 所以选择了 0.18 版本
$ npm install -g hexo-cli@0.1.8

# 新建一个 hexo
$ hexo init hexo
$ cd hexo && npm install

# 安装 hexo-math
$ npm install hexo-math --save 

# 安装 hexo-deployer-git 
$ npm install hexo-deployer-git --save
```
这样就搞定了
接下来让我们测试一下

``` bash
# 新建一篇文章
$ hexo n "test"

# 在文章中输入
$ e^{\pi i} = cosx - isinx $

# 之后生成 html
$ hexo g

# 本地启动测试
$ hexo s
```
如果能看到渲染后的公式就说明成功了
$ e^{\pi i} = cosx - isinx $

## 之后的计划
目前来看默认的模板已经够用了, 以后再来折腾吧.
这里以后会分享一点看得书以及最近折腾的一些东西. 主要是做个记录.
