---
title: 如何在github上创建个人博客
date: 2018-08-01 12:26:22
tags:
  - github
  - hexo
<<<<<<< HEAD
cover: https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/Softonic-image-1.png
=======
>>>>>>> c629bc89298d7c1433ea9efae008a21745e45aa8
---
  因缘巧合之下看到有人利用博客记录每天的学习笔记，觉得这也不失为一个学习提升自己的好方式，希望能借此激励自己每天持续学习和思考。于是看了很多网上的教程做准备，进行第一次搭建个人博客，写下本文总结搭建的过程以及遇到的一些问题。
  本文主要目的是快速搭建，适合那些只是用来记录一些东西，不想做太多折腾的小白。
<!--more-->

----------
----------
----------
# 一、前言 #
先说下自己搭建博客的大致流程：
1. 登录GitHub，创建仓库。
2. 在pc上安装Git，node.js，Hexo环境。
3. 博客配置和主题优化（按需配置）。
4. 绑定自己的域名（可选）。


----------

# 二、准备工作 #
 
## 1. 创建GitHub仓库 ##
  登录GitHub账号（注册请自己查找网上教程），右上角用户头像处选择your repositories点击new创建一个仓库，注意：仓库名的格式，必须是yourusername.github.io；
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/92394050.jpg)

## 2. Git安装与配置 ##
### 2.1 Git安装 ###
Windows：[git-for-windows](https://gitforwindows.org/)
下载完成后，具体安装过程见[Git 中文详细安装教程](https://blog.csdn.net/sishen47k/article/details/80211002)

- **这里推荐一个神器：[cmder](https://jeffjade.com/2016/01/13/2016-01-13-windows-software-cmder/)，功能强大，比windows自带的cmd好用很多。（注：cmder最好解压放在磁盘的根目录下）**

### 2.2 Git配置 
Git安装完成后，打开Git Bash，第一次打开需要设置username和email；
`git config --global user.name "YiYunC"   #与注册的账号邮箱一致；`
`git config --global user.email "chenyiyun940920@gmail.com"`

在F盘新建一个名为blog的文件夹作为仓库，在命令行里输入：
`cd /f/blog    #进入F盘的blog文件夹；`
`git init      #初始化当前文件夹作为本地仓库（生成.git文件夹）;`

### 2.3 配置SSH keys ###
先检查下电脑用户主目录下，有没有.ssh文件夹，看看有没有id_rsa和id_rsa.pub，有的话直接跳到下一步。
如果没有，在Git Bash里输入命令，生成新的SSH Key:
`ssh-keygen -t rsa -C "chenyiyun940920@gmail.com"`
`Generating public/private rsa key pair.`
`Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>`
接下来就是设置加密密码串（6位），可以不设置。
ssh key设置成功的界面忘记截图了。
上述步骤以及添加SSH Key到GitHub的步骤详见[利用Github免费搭建个人主页(个人博客)](https://blog.csdn.net/hitwhylz/article/details/42646197)


## 3.Node.js安装 ##
到Nodejs[官网](https://nodejs.org/en/)下载，默认安装即可。

----------

## 4.Hexo安装与配置 ##
### 4.1 Hexo安装 ###
`npm install -g hexo-cli`
### 4.2 Hexo配置 ###
`cd /f/blog`
`hexo init blog`
`hexo g #启动本地服务器`
`hexo s #在浏览器输入http://localhost:4000/就可以看见网页和模板了`
注意：进入http://localhost:4000/页面前，Git Bash里不要按下ctrl+c和ctrl+v，不然该页面将停止加载。

----------

# 三、博客配置和主题优化 #
## 1. 博客配置 ##
用文本编辑器打开blog根目录下的**_config.yml**文件，我自己用的是[Notepad++](https://pan.baidu.com/s/15yqX5us7xbnveMH6fb9oSg)，其实用[Sublime Text](http://www.sublimetext.com/3)会更好，因为Sublime Text默认打开格式是UTF-8编码，而Notepad++还需要手动设置；一般博客中文出现乱码，大多数是因为配置文件的格式不是URF-8编码。

打开之后，必须要改的有：
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/97482861.jpg)
注：**每个：后面必须有一个空格；还有language的设定是由主题决定，后面会讲解。**

![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/61882237.jpg)
url填写的地址在GitHub里刚才新建的仓库里，点击setting，拉到下面可以看见GitHub Pages里有域名地址，一般默认是GitHub提供的二级域名github.io，这里我已经更换了.me域名。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/66456455.jpg)

最后这里的一段很重要，改成我图片上的，repo上的地址就是你的GitHub的仓库地址。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/59423392.jpg)

这里说一下，博客根目录下的_config.yml文件叫做**站点配置文件**；而主题目录里的_config.yml文件叫做**主题配置文件**。

## 2. 主题配置 ##
我用的是Next主题，其风格简约并且功能比较完善，加上时间仓促所以我选择了这个，但是也花了我好多时间来配置完善一些基础功能。还有一些好看的主题如yilia，其实有时间去配置一下，自己的博客看起来会更上一个档次，只能等我以后工作稳定了慢慢去修改了。
Next主题下载并解压后，将其放在theme目录里，同时站点配置文件里设置`theme: “主题文件夹的名字”`,就能切换到相应主题，主题配置文件里可以设置的有很多，就不一一阐述了，讲几个比较偏的。

1. 主题风格切换（共四种）
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/88249424.jpg)
2. 头像以及博客图标的更改
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/46777381.jpg)
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/52227617.jpg)
3. 打赏的二维码
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/61020954.jpg)

注：要设置的图片均放在主题目录里的**/source/images**里

<<<<<<< HEAD
- 这里给出我自己设置好功能尚完整的Next主题[文件](https://pan.baidu.com/s/1WMp5Sj9n8BiX1HQ04d7aew)。
=======
- 这里给出我自己设置好的Next主题[文件](https://pan.baidu.com/s/1WMp5Sj9n8BiX1HQ04d7aew)，该有的基本都有了，其他的留待你自己去完善。
>>>>>>> c629bc89298d7c1433ea9efae008a21745e45aa8

----------

# 四、绑定域名 #
这一步不是必须的，但是绑定域名便于记忆，如果你不愿意每次进入博客都输入github.io这样长长的二级域名，那就去买一个域名绑定下吧，反正也不是特别贵。
我用的GitHub免费送的一年[namecheap](https://www.namecheap.com)的.me域名，设置下DNS就可以了。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-4/73143893.jpg)
其他的域名绑定设置其他教程里也有。

----------

# 五、基本操作 #
`hexo n “新文章” == hexo new “新文章”  #新建文章`
新建文章，生成md文件，可以用[MarkdownPad2](http://markdownpad.com/)进行编辑。

每次修改完文章或主题什么的，进行以下操作：
本地预览：
`hexo clean`
`hexo generate #简化命令 hexo g`
`hexo server #简化命令 hexo s`
确认没有需要修改的地方后，上传部署：
`hexo clean`
`hexo generate #简化命令 hexo g`
`hexo deploy #简化命令 hexo d`
其实也可以把指令集合成批处理命令;
`hexo clean & hexo g & hexo s`
`hexo clean & hexo g & hexo d`
注：** 上传部署后，博客并不是马上就能更新的，需要等待一段时间 **

文章需要加入图片的话，可以用[极简图床](http://jiantuku.com/#/)，免费申请七牛云，注册时有提供教程（身份检核时间挺长的）。
<<<<<<< HEAD
** 很生气，极简图床很有可能删库跑路了，之前文章插入的图片都没有了！！！伤心啊。所以想着能不能自建图床，看了这个人的文章，搭建了github图床，背靠github和微软总不会挂了吧。**

结束语：有些东西没有详细说，以后慢慢更新。

2019.4.1更新：
电脑之前换了固态，加上换工作，生活所迫，很长一段时间没有来管理博客，这样就要考虑到换电脑后个人博客应该如何维护。
由于之前自己有在github创建备份分支backup，所以接下来操作很简单。
暂待更新.......
=======

结束语：有些东西没有时间详细说了，以后慢慢更新，并且很多博客写得比我好多了，下面给出一些参考文章，都是会用到的东西。
>>>>>>> c629bc89298d7c1433ea9efae008a21745e45aa8

参考文章：
1. [如何在github创建个人博客？](https://blog.csdn.net/a1274624994/article/details/54749094)
2. [GitHub+Hexo搭建及优化](https://hceng.cn/2017/03/06/GitHub+Hexo%E6%90%AD%E5%BB%BA%E5%8F%8A%E4%BC%98%E5%8C%96/)
3. [利用Github免费搭建个人主页(个人博客)](https://blog.csdn.net/hitwhylz/article/details/42646197)
4. [Hexo的Next主题详细配置](https://www.jianshu.com/p/3a05351a37dc)
5. [hexo的next主题个性化教程:打造炫酷网站](http://shenzekun.cn/hexo%E7%9A%84next%E4%B8%BB%E9%A2%98%E4%B8%AA%E6%80%A7%E5%8C%96%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B.html)
6. [【工具】用hexo搭建博客](https://blog.csdn.net/shanchuan2012/article/details/53572235)
7. [Hexo | （二）Next主题个性化设置](http://www.cnblogs.com/liziczh/p/9318656.html)
8. [Hexo | （三）URL优化&站点提交](https://www.cnblogs.com/liziczh/p/9318665.html)
9. [Hexo | （四）多机同步更新博客](https://www.cnblogs.com/liziczh/p/9318670.html)



