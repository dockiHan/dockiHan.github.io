---
title: Build-Blog-1
date: 2018-12-27 11:57:40
tags: [Hexo]
categories: [Hexo]
---

# 使用GitHub Pages 与 Hexo 搭建个人博客站点（一）


---
## 前言

谨以此文记录本人的博客建站历程，期间参考了众多前辈的建站经验，在后续正文中会做相关的引用说明。

作为一枚拖延症与懒惰症的晚期患者，最近突发奇想，想要写点东西来记录自己的学习点滴，于是乎开始了个人博客的踩坑之路......

本教程所涉及的各种工具及相应的版本说明

**Requirements：<br/>
操作系统： MacOS <br/>
Git: git version 2.15.2 (Apple Git-101.1) <br/>
NodeJs: 10.4.1 <br/>
Npm: 6.5.0 <br/>
Hexo: 3.8.0 <br/>
Next: v6.6.0  (直接git pull最新版)**

---
## 起航
* 首先去GitHub新建一个代码仓库，名称必须是userName(gitHub用户名).github.io

![图-1][1]


  注意勾选 **Initialize this repository with a README**
* 进入刚刚新建的代码库，新建一个远程分支（假设新分支名称为build）用于存储建站所需的内容。
（由于GitHub Pages只能自动识别master分支，因此代码库的master分支只能用来存放博客部署的相关内容而不能存放其他内容，如果master分支放了额外内容则GitHub Pages会在生成页面时报错，导致个人主页404。我在这里被坑了好多次才顺利通过）
* 配置 GitHub SSL
 * 执行 `ssh-keygen -t rsa -C "<密钥值>"` （<>内表示可以自由填写的内容）
 * 连按三次回车键直至生成密钥
 * SSH KEY 生成之后会默认保存在 ~/.ssh 目录中，打开这个目录，cat 一下 id_rsa.pub 文件，复制全部内容，即复制密钥。
 * 打开 GitHub，依次点击 头像 --> Settings --> SSH and GPG keys --> Add SSH key，将复制的密钥粘贴到 key 输入框，最后点击 Add Key ，SSH KEY 配置成功，如下图所示：

![图-5][2]

* 在本地执行 git pull 拉下代码库：
 * 拉取代码库
 `git pull https://github.com/userName/userName.github.io.git`
 * 新建本地分支build
 `git checkout -b build`
 * 为本地的build分支设置上游分支，设置为远程的build分支
 `git branch --set-upstream-to origin build`
* 在 GitHub 中进入代码库，在Settings中找到 GitHub Pages 将 Source 设置为 master 分支（如图）。然后在下面选择一个主题（可选操作，对后续影响不大）。

![图-2][3]

![图-4][4]

* **注意时刻保持远程仓库与本地仓库的一致性，git 产生分歧后将会比较难处理。（后续操作中一定要保证 build 分支的一致性， master 分支在这里作为博客部署分支，可以不用在本地手动 git pull，后面介绍的 hexo-deployer-git 会去处理 master 分支）**
如果远程仓库有了改变，在本地的仓库执行 `git pull` 拉取最新的内容
如果本地仓库有了改变， 依次执行 `git add <your file name> ` `git commit -m "<your commit message>" ` 和 `git push `
同时注意当前的本地分支，在本地 git 仓库会都有所标注（如图所示，当前操作的分支即是本地的build分支）

![图-3][5]

切换当前分支可使用命令`git checkout <branch name>`
其他有关Git的操作这里就不再赘述，可以自行 Google 或者百度
* 安装 Hexo 以及相关依赖
 * 安装 Node JS [官方传送门][6]
 * 安装 Hexo `npm install -g hexo-cli` 如果 npm 执行较慢可先更换一下镜像。 使用临时源（阿里源）`npm --registry https://registry.npm.taobao.org install -g hexo-cli` 其他 npm 镜像相关操作可参见https://www.jianshu.com/p/f311a3a155ff
 * 在 build 分支下初始化hexo `hexo init hexo`
 * 进入hexo目录，安装相关依赖

```bash
 cd hexo
 npm install
 hexo generate
```


* 启动 hexo 本地 server  `hexo server`
* 浏览器访问 **lcalhost:4000** 即可看到初始化的博客页面了（按 Ctrl + C 可停止 hexo 本地服务器）
* 配置 hexo 配置文件
进入 hexo 目录，打开 _config.yml 配置文件做如下配置

```yaml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/
# Site
title: Docki's Home
subtitle: 非淡泊无以明志，非宁静无以致远
description: 走在求知的路上
keywords:
author: docki
language: zh-CN
timezone: Asia/Shanghai
# URL
## If your site is put in a subdirectory,
## set url as 'http://yoursite.com/child' and root as '/child/'
url: https://dockiHan.github.io
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:
```

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
    type: git
    repository: https://github.com/yourName/yourName.github.io.git
    branch: master
    message: hexo depoly
```
* 执行下面的命令，安装 hexo-deployer-git 插件，快速把代码托管到 GitHub 上
`npm install hexo-deployer-git`
（安装速度慢可更换npm镜像，--见前文）
* 执行下面的代码命令，将 hexo 项目托管到 GitHub 上，Hexo 会通过上面配置文件中的 # Deployment 相关配置进行部署。

```
hexo clean  //清空当前缓存
hexo d -g   //d 为deploy的缩写，g为generate的缩写，表示生成静态页面并部署
```

* 至此一个简单的个人博客站点就搭建完成了，浏览器访问**https://userName.github.io**就可以看到已经部署的页面了
* 特别鸣谢各位探路前辈：
 * [传送门1][7]
 * [传送门2][8]


---
## 进阶 
目前肯定会有很多小伙伴不满足于现状，想要自己的博客站点变得更酷一些、更炫一点，最简单的方法就是去找一些前端大神实现好的主题，然后在此基础上做一些个性化的定制，这里就以人气值最高的Next主题为例......

* 下载 Next 主题
官方传送门：[GitHub][9]
这里推荐使用 git 方式安装，后续更新非常方便
* 修改全局配置文件（ hexo 目录下的 _config.yml ）
将 Extension 片段的 Theme 改为 next 如下

```yaml
 # Extensions
 ## Plugins: https://hexo.io/plugins/
 ## Themes: https://hexo.io/themes/
 theme: next
```

* 修改 Next 主题的配置文件（ themes/next/ 目录下的 _config.yml ）


  [1]: https://raw.githubusercontent.com/dockiHan/dockiHan.github.io/build/hexo/source/post_images/Build-Blog-1/pic_1.png
  [2]: https://raw.githubusercontent.com/dockiHan/dockiHan.github.io/build/hexo/source/post_images/Build-Blog-1/pic_5.png
  [3]: https://raw.githubusercontent.com/dockiHan/dockiHan.github.io/build/hexo/source/post_images/Build-Blog-1/pic_2.png
  [4]: https://raw.githubusercontent.com/dockiHan/dockiHan.github.io/build/hexo/source/post_images/Build-Blog-1/pic_4.png
  [5]: https://raw.githubusercontent.com/dockiHan/dockiHan.github.io/build/hexo/source/post_images/Build-Blog-1/pic_3.png
  [6]: https://nodejs.org/zh-cn/
  [7]: https://mp.weixin.qq.com/s?__biz=MzU5MTE0ODcwNQ==&mid=2247484100&idx=1&sn=5051ed4c889747259fa00765a1286f62&chksm=fe32210ac945a81c2ce6e8dbaefebc1f44fbde6de3642fe1d61e2e61d292ffa32277dae5528d#rd
  [8]: https://zhuanlan.zhihu.com/p/35668237
  [9]: https://github.com/iissnan/hexo-theme-next
