title: 'HEXO+Github,搭建属于自己的博客'
categories: 手册
date: 2017-05-18 10:08:21
tags:
---
前言
=============

一直都想拥有个属于自己的博客，之前买过域名做过静态展示类型的个人网站，苦于那时的自己对数据库操作不怎么了解，所以没有将其继续发展成动态网站并且维护下去，那时觉得自己需要搭建个动态博客，应该不只要会前端，后台至少也应该要懂些。  
直到偶然的机会让我遇到了[Hexo](https://hexo.io)。   
Hexo+Github的结合让谁都能轻轻松松搭建起属于自己的博客，相当COOL，相信不久的将来，Hexo就会流行起来。   
Hexo出自于台湾大学生[tommy351](http://twitter.com/tommy351)之手，基于node.js环境的静态博客程序，Hexo生成的静态网页可以直接放到GitHub Pages、BAE、SAE等平台上。大概生活中所有的创新都来源于对现状的不满意，[Hexo 颯爽登場！](https://zespia.tw/blog/2012/10/11/hexo-debut/)一篇来自[tommy351](http://twitter.com/tommy351)对[Octopress](http://octopress.org/)的吐槽。

因为我Github的username为wenlisu，所以以下事例以wenlisu代替，宝宝们要将其换成自己Github的的username。

 环境准备
=============

## 安装node

到[Node.js](https://nodejs.org/en)官网下载相应平台的最新版本，一路安装即可，比较喜欢折腾的同学可以通过安装[nvm](https://github.com/creationix/nvm)或者n`n是Node的一个模块`来安装或管理node版本，体验也是相当不错的，我是用的nvm`^o^推荐`。

## 安装Git

目前市面上也有图形化界面版，例如[SmartGit](http://www.syntevo.com/smartgit/download)，挺方便直观的。  
如果想要看起来高大上点的话，可以选择`shell`，可以在上面输入要执行的命令。首先，你可以试着输入git，看看系统有没有安装Git。

```
$ git
The program 'git' is currently not installed. You can install it by typing:
sudo apt-get install git
```

假如没有安装，返回的信息提示通过一条`sudo apt-get install git`就可以直接完成Git的安装，非常简单。

 Github
=============

```
* 首先注册一个『GitHub』帐号。
* 建立与你用户名对应的仓库，仓库名为『your_user_name.github.io』。
* 添加SSH公钥到『Account settings -> SSH Keys -> Add SSH Key』。 
```
建立与你用户名对应的仓库：

![](http://ww3.sinaimg.cn/large/006tKfTcgy1ffplg4xs2kj30xp0kb40y.jpg)

不知道宝宝们有没有遇到过这种情况，新建仓库后，在命令行中按照所给提示：输入以下命令时，会报错如下：

```
Warning: Permanently added the RSA host key for IP address '192.30.252.130' to the list of known hosts.
Permission denied (publickey).
fatal: Could not read from remote repository.
```

所以主要讲第三步，怎么给Github添加SSH-Key，宝宝们，我是一路踩着坑过来的`( ´▽｀)`。

## 首先设置你的用户名密码：

```
$ git config --global user.email "415357008@qq.com"
$ git config --global user.name "wenlisu"
```
## 生成密钥：

```
$ ssh-keygen -t rsa -C "415357008@qq.com"
```

输入文件路径,一路默认`<Enter>`,当提示`Enter passphrase`是让你输入密码，假如输入，以后每提交一次就需要输入一次密码，反正我是不可能输密码的，每次都需要，多麻烦啊。

```
webdevs-MacBook-Pro:wenlisu.github.io webdev$ ssh-keygen -t rsa -C "415357008@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/webdev/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
```

## 将SSH密钥复制到剪贴板。

```
$ pbcopy < ~/.ssh/id_rsa.pub
```

接下来去到Github，点击`settings`。

![](http://ww4.sinaimg.cn/large/006tKfTcgy1ffpmwc2yyjj30vd0jqdlr.jpg)

按照以下操作给SSh key添加title（例如Personal MacBook）， 再将剪贴板的内容粘贴到key中，点击`Add SSH key按钮`。

![](http://ww4.sinaimg.cn/large/006tKfTcgy1ffpmx1sdisj30uk0e3taf.jpg)

至此，Github中的SSH已添加完毕，具体疑问可到[Github](https://help.github.com/categories/authenticating-to-github/)中查询。

## 测试一下该SSH key
```
$ ssh -T git@github.com
```

当你输入以上代码时，会有一段警告代码，如：

```
The authenticity of host 'github.com (207.97.227.239)' can't be established.
# RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
# Are you sure you want to continue connecting (yes/no)?
```

这是正常的，你输入 yes 回车既可。如果你创建 SSH key 的时候设置了密码，接下来就会提示你输入密码，如：

```
Enter passphrase for key '/c/Users/Administrator/.ssh/id_rsa':
```

密码正确后你会看到下面这段话，如：

```
Hi username! You've successfully authenticated, but GitHub does not
# provide shell access.
```

如果用户名是正确的,你已经成功设置SSH密钥。如果你看到 “access denied” ，则表示拒绝访问，那么你就需要使用 https 去访问，而不是 SSH 。

接着下来安装Hexo。

 Hexo
=============

前提是必须先安装 [Node.js](https://nodejs.org/en)，在安装node.js过程中不懂的尽情问Google吧。   

```
$ npm install hexo -g
```

## 初始化

如果制定 `<folder>`，便会在目前的文件夹下建立一个名为 `<folder>` 的新文件，否则会在目前文件下初始化。

```
$ hexo init <folder>
$ cd <folder>
```

## 安装依赖包

```
$ npm install
```

## 生产静态文件 && 服务

```
$ hexo generate || hexo g 
$ hexo server   || hexo s 
```

启动本地服务器，默认情况下，打开`http://localhost:4000/`。  
到目前状态下，个人博客雏形基本已经完成，接下来，将如何把Hexo生成的静态网页内容目录放到Github上，打开博客根目录下的_config.yml在最下方加上。

```
deploy:
     type: git
     repo: https://github.com/wenlisu/wenlisu.github.io.git
     branch: master
```

每一步的部署，按照以下三个步骤进行

```
$ hexo clean
$ hexo generate
$ hexo deploy
```
当执行`hexo deploy`命令部署到GitHub上的内容目录啦，这时候打开`https://wenlisu.github.io`就将是自己的博客地址啦，是不是很方便快捷啊，宝宝们赶紧行动起来吧！

## 更新20180126

* Deployer not found: git  
在终端执行命令：
```
npm install hexo-deployer-git --save
```
然后继续执行hexo deploye指令进行部署。

* hexo deploy部署上github后，在github上显示404，但在本地测试正确。  
极其可能是你的仓库名字有问题，首先你要清楚你的Github的用户名userName，然后这个仓库的名字必须是你的userName.github.io ,仓库名必须跟你的github用户名一致，不然会出现错误404的页面。
