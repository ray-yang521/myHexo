---
title: Hexo博客系统的搭建
author: YangChongZhi
date: 2020-02-20 15:53:33
id: 1
categories: EnvConfig
tags: [EnvConfig,Windows]
top: True
cover: True
---

## 引言：
Hexo可以理解为是基于node.js制作的一个博客工具，不是我们理解的一个开源的博客系统。通过markdown编写文章，然后hexo帮我们生成静态的html页面，然后，将生成的html上传到我们的服务器，或上传到Github、Gitee等仓库托管平台，在开启该类仓库的Pages服务后便可使用。简而言之：hexo是个静态页面生成、上传的工具。
<!-- more -->

### Hexo简介
Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub、Gitee或Coding上，是搭建博客的首选框架。

### Hexo的搭建步骤
- 安装Git
- 安装Node.js(其中就包含了npm)
- 本地安装Hexo
- 验证是否能在本地浏览器访问Hexo博客系统
- 使用Github创建个人仓库
- 使用Gitee创建个人仓库
- 使用Coding创建个人仓库
- 生成SSH添加到Github,Gitee或Coding中
- 将本地的Hexo部署到以上创建的仓库中（可以同时上传到多个仓库中）
- 开启各个仓库的Pages服务（免费的）
- 访问个人博客（可以开始使用了）
- 本地写文章
- 同步上传到所需要部署的仓库中

### 一、安装Git
[Git官网地址](https://git-scm.com/downloads)

### 二、安装Node.js
[nodejs官网地址](https://nodejs.org/zh-cn/)

### 三、本地安装Hexo
前面的Git和Nodejs安装好之后，就可以正式安装Hexo了。

建议在执行后续的安装命令时将npm的安装源更换成国内的镜像（淘宝的镜像），不然后续用到的npm安装命令都会很缓慢，而且可能还会在中途卡死，所以建议换源。使用以命令设置npm的镜像源：(也可以手动打开“C:\Users\Administrator”目录下的“.npmrc”文件，增加“registry=https://registry.npm.taobao.org” 即可，其中“Administrator”为你的用户名)
```bash
npm config set registry https://registry.npm.taobao.org
```
使用如下命令查看npm是否换源成功：
```bash
npm config get registry
```
若显示“https://registry.npm.taobao.org”，则说明换源成功！

npm换源成功后，使用如下命令安装Hexo到本地，别忘了“-g”这个参数：
```bash
npm install -g hexo-cli
```
之后可以在任意文件目录中创建一个文件夹用来存放你的博客项目，比如名为 myblog,然后利用命令提示符的cd命令进入到该文件夹下，利用hexo来初始化你的博客系统：
```bash
hexo init  # 注意，该命令一定是要在你新建的那个文件夹目录下执行。
```
或者你也可以直接使用“hexo init myblog”命令，该命令会直接在当前目录下创建名为“myblog-hexo”的文件，并初始化项目。（其实hexo init命令本质上是利用Git从远端克隆项目到本地）

如果初始化过程中没有出现“error”（忽略WARN），那么初始化后，你的文件目录看起来应该是这样的：
![image](Hexo博客系统的搭建/1.png?v=1&type=image)
如果以上步骤一切正常，那么Hexo在本地的安装就成功了。

### 四、验证是否能在本地浏览器访问Hexo博客系统
继续在以上该项目目录下，运行以下命令：
```bash
hexo clean
hexo g
hexo s
```
- hexo clean 命令用来删除之前生成的前端页面，即public文件夹，此时由于还没有任何前端界面，该目录下没有public文件夹。
- hexo g 命令全称hexo generate，用来生成前端访问的界面，生成的文件会存放在“public”文件夹中
- hexo s 命令全称hexo server 用来开启本地的Hexo服务，开启后可在本地通过浏览器访问生成的页面

运行以上三条命令后，可在本地打开浏览器，输入网址**127.0.0.1:4000**即可看到你生成的博客页面了，大概长这样：
![image](Hexo博客系统的搭建/2.png?v=1&type=image)
可以使用 Ctrl+c 把hexo服务关掉。
至此，Hexo博客系统才算真正在本地安装成功了。

### 五、使用Github创建个人仓库
到 [Github官网](https://github.com/) 注册账号，并记住你的username和邮箱，username最好起得有个性，后面会用到。注册后应该是可以使用 “https://github.com/你的username” 访问到你的个人主页，该主页会显示出你的代码提交量情况和公开仓库等个人信息。比如我的username为yczlab,那么可以通过https://github.com/yczlab 访问到我的个人主页。
**注册并登录你的Github后，如下图，点击右上角 New repository，新建仓库：**
![image](Hexo博客系统的搭建/3.png?v=1&type=image)
**进行仓库创建时的设置：**
![image](Hexo博客系统的搭建/4.png?v=1&type=image)
**创建好该仓库后，进入该仓库,里面暂时只有一个README文件：**
![image](Hexo博客系统的搭建/5.png?v=1&type=image)
**点击该仓库右上角的Settings，找到Github Pages:**
![image](Hexo博客系统的搭建/6.png?v=1&type=image)

### 六、使用Gitee创建个人仓库
到 [Gitee官网](https://gitee.com/) 注册账号(Gitee是中国的类似于Github的代码托管平台，访问速度会比Github快很多，建议使用该平台来搭建Hexo)，并记住你的个人空间地址和邮箱，个人空间地址最好起得有个性，后面会用到，这里的个人空间地址并不是你的姓名。（个人空间可以在登录后，点击右上角头像选择“设置”，再选择“个人空间地址”查看并修改）注册后应该是可以使用 “https://gitee.com/你的个人地址空间” 访问到你的个人主页，该主页会显示出你的代码提交量情况和公开仓库等个人信息。比如我的个人地址空间为yczlab,那么可以通过https://gitee.com/yczlab 访问到我的个人主页。
**注册并登录你的Gitee后，如下图，点击右上角，新建仓库：**
![image](Hexo博客系统的搭建/7.png?v=1&type=image)
**进行仓库创建时的设置：**
![image](Hexo博客系统的搭建/8.png?v=1&type=image)
**同样创建好该仓库后，进入该仓库，里面暂时只README文件：**
![image](Hexo博客系统的搭建/9.png?v=1&type=image)
**点击该仓库右上角的“服务”，再点击“Gitee Pages”:**
![image](Hexo博客系统的搭建/10.png?v=1&type=image)

### 七、使用Coding创建个人仓库
[Coding的官网](https://coding.net/)，在Coding中注册、登录、创建仓库，步骤与上面大同小异，这里就不在继续介绍了。本文主要介绍如何利用Hexo将博客系统同时部署到Github和Gitee中。

### 八、生成SSH公钥添加到Github,Gitee或Coding中
以下方法为在Windows中生成并使用SSH工具

- **生成ssh公钥添加到Github**
保证 Git 已经正确安装好。进入“C:\Users\Administrator\.ssh”目录下（其中Administrator为你的用户名），鼠标右键点击打开“Git Bash Here”，输入以下命令，并连续按三次回车键：
```bash
ssh-keygen -t rsa -C "你的邮箱名称" -f "产生的公私钥名称"
```
其中“你的邮箱”为你注册Github时使用的邮箱地址，比如QQ邮箱；“产生的公私钥名称”为你想要为其命名的名称，若不命名，则会使用默认的名称，会覆盖以前也使用默认名称的公私钥文件。比如我执行 'ssh-keygen -t rsa -C "2685733832@qq.com" -f "github_rsa" '，结果如下：
![image](Hexo博客系统的搭建/11.png?v=1&type=image)
此时会在当前目录下生成两个文件“github_rsa”和“github_ras.pub”，一个为私钥（不要公开），另一个为私钥（可以公开）：
![image](Hexo博客系统的搭建/12.png?v=1&type=image)
用自己注册的账号登录Github官网，点击右上角的"Settings"——>点击"SSH and GPG keys"——>点击“New SSH key”，添加刚刚生成的公钥：
![image](Hexo博客系统的搭建/13.png?v=1&type=image)
现在还不能正常使用，因为在新生成这个公私钥文件的时候不是采用的默认命名，而是带了“-f”参数对该公私钥文件进行了重命名，所以本地的ssh服务可能就找不到我们新生成的公私钥文件，所以还需要在本地配置一下，才能正常使用。在该公私钥文件所在的目录下新建一个名为“config”的文件(注意该文件没有后缀)，往该文件中写入以下的信息，并保存：
```bash
# Github的公钥配置
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_rsa #注意，最后这个地方换成你在生成该公私钥时的命名，比如我刚刚在生成时用的github_rsa，这里我就写成github_rsa
```
输入以下命令，验证是否可用：
```bash
ssh -T -v git@github.com
```
显示以下信息，说明验证成功，可以使用：
![image](Hexo博客系统的搭建/14.png?v=1&type=image)
如果该过程没有成功，建议百度自行解决一下。
至此，SSH公钥就成功添加到Github，并可以使用了。可以使用git命令对Github中的仓库进行克隆和上传代码。

- **生成ssh公钥添加到Gitee**
方法和上面的差不多，只是在生成的公私钥的时候注意命名不要和上面已有的公私钥名称重复了，邮箱（注册Gitee账号时用的邮箱）可以重复。比如：
```bash
ssh-keygen -t rsa -C "2685733832@qq.com" -f "gitee_rsa"
```
![image](Hexo博客系统的搭建/15.png?v=1&type=image)
此时会在当前目录下生成两个文件“gitee_rsa”和“gitee_ras.pub”，一个为私钥（不要公开），另一个为私钥（可以公开）：
![image](Hexo博客系统的搭建/16.png?v=1&type=image)
用自己注册的账号登录Gitee官网，点击右上角的"头像"——>点击"设置"——>点击“SSH公钥”，添加刚刚生成的公钥：
![image](Hexo博客系统的搭建/17.png?v=1&type=image)
同样，现在还不能正常使用，还需要在本地配置一下，才能正常使用。在该公私钥文件所在的目录下新建一个名为“config”的文件(注意该文件没有后缀)，如果已有那就不用新建，往该文件中写入以下的信息，并保存：
```bash
# Gitee的公钥配置
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_rsa #注意，最后这个地方换成你在生成该公私钥时的命名，比如我刚刚在生成时用的gitee_rsa，这里我就写成gitee_rsa
```
输入以下命令，验证是否可用：
```bash
ssh -T -v git@gitee.com
```
显示以下信息，说明验证成功，可以使用：
![image](Hexo博客系统的搭建/18.png?v=1&type=image)
如果该过程没有成功，建议百度自行解决一下。
至此，SSH公钥就成功添加到Gitee，并可以使用了。可以使用git命令对Gitee中的仓库进行克隆和上传代码。

- **生成ssh公钥添加到Coding**
该过程基本和上面的步骤一样，如有需要，请参照上面的步骤自行添加ssh公钥到Coding中。

### 九、将本地的Hexo部署到以上创建的仓库中（可以同时上传到多个仓库中）
在创建好Hexo想要部署到的平台的相应仓库后，便可以配置本地已安装好的Hexo博客系统，使本地的博客系统可以上传到相应的仓库，并供大家访问博客。

打开本地本地Hexo项目中的配置文件，即“_config.yml”文件，修改其中的以下内容，添加自己想要上传到的平台仓库中（这里我添加了两个,gitee和github）：
![image](Hexo博客系统的搭建/19.png?v=1&type=image)
然后打开命令提示符，利用cd命令进入该目录下，输入以下命令将本地项目上传到以上指定的仓库中：
```bash
hexo g #生成静态页面至public目录
hexo d #将.deploy_git目录部署到指定仓库地址
```
可能 "hexo d" 命令初次运行时会报错，那是因为该项目缺少”hexo-deployer-git"插件，在该项目中使用以下命令安装即可：
```bash
npm install hexo-depoyer-git --save
```
然后再运行“hexo d”命令，该命令的执行速度可能有点慢，该命令本质是将本地的文件通过git上传到远端，可能中途还会失败。如果失败，就一直执行，直到成功即可。

### 十、开启各个仓库的Pages服务（免费的）
关于各个仓库如何开启Pages服务，在上面创建仓库的步骤中就已说明，这里就不在重复。Github时默认开启Pages的，而Gitee在每次上传后都需要手动刷新。

### 十一、访问个人博客（可以开始使用了）
成功上传后，就可以根据Pages服务中提供的地址访问你的博客了。
比如我的地址：
Gitee地址：[https://yczlab.gitee.io/](https://yczlab.gitee.io/)
Github地址：[https://yczlab.github.io/](https://yczlab.github.io/)

### 十二、本地写文章
在本地的Hexo项目文件中利用一下命令生成一篇新的博客：
```bash
hexo n "博客名称"
```
以上命令执行后便会在“source\_posts\”目录中生成一篇博客文章，可利用MarkDown语法来书写你的博客文章。

### 十三、同步上传到所需要部署的仓库中
在你的文章写好后，执行以下命令同步上传到部署平台上：
```bash
hexo g
hexo d
```
注意：Gitee的Pages服务需要每次上传后手动刷新。