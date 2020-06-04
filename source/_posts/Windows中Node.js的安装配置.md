---
title: Windows中Node.js的安装配置
author: YangChongZhi
tags: [EnvConfig,Windows]
date: 2020-02-22 12:50:56
id: 1
categories: EnvConfig
top: True
---
## 引言：
Window中Node.js及npm详细安装配置过程，npm是Nodejs下的包管理工具，在安装Nodejs时，会自动安装npm。
<!-- more -->

### 一、安装过程

#### 官网下载 Nodejs 的安装包：
![image](Windows中Node.js的安装配置/1.png?v=1&type=image)

#### 双击安装：
![image](Windows中Node.js的安装配置/2.png?v=1&type=image)

![image](Windows中Node.js的安装配置/3.png?v=1&type=image)

![image](Windows中Node.js的安装配置/4.png?v=1&type=image)

![image](Windows中Node.js的安装配置/5.png?v=1&type=image)

![image](Windows中Node.js的安装配置/6.png?v=1&type=image)

![image](Windows中Node.js的安装配置/7.png?v=1&type=image)

![image](Windows中Node.js的安装配置/8.png?v=1&type=image)

**点击 Finish 完成安装。**

#### 安装完成后目录如下：
![image](Windows中Node.js的安装配置/9.png?v=1&type=image)

#### 安装完成后的环境变量：
安装完成后，环境变量会自动添加。此时系统环境变量的 Path 中会多出“D:\nodejs\”这么一条信息；用户环境变量的 Path 中会多出“C:\Users\Admin\AppData\Roaming\npm”这么一条信息。
windows+r键，打开cmd

![image](Windows中Node.js的安装配置/10.png?v=1&type=image)

检查 node 和 npm 的版本，查看是否可用：

![image](Windows中Node.js的安装配置/11.png?v=1&type=image)

### 二、环境配置详细过程

**1、再看看另外2个目录，npm的本地仓库跑在系统盘c盘的用户目录了(没见到npm-cache是因为没有用过，一使用缓存目录就生成了)，这里我们将把这2个目录移动回到D:\nodejs中**
![image](Windows中Node.js的安装配置/12.png?v=1&type=image)

先如下图，在D:\nodejs中建立2个目录

![image](Windows中Node.js的安装配置/13.png?v=1&type=image)

运行以下两条命令，设置npm的本地仓库和缓存目录：
```bash
npm config set prefix "D:\nodejs\node_global"
npm config set cache "D:\ndoejs\node_cache"
```
以上命令运行后，可以通过 **npm config list** 命令显示npm的所有配置信息：

![image](Windows中Node.js的安装配置/14.png?v=1&type=image)

也可以直接打开 npm 的配置文件，直接修改其中的内容：

![image](Windows中Node.js的安装配置/15.png?v=1&type=image)

**2、换源（因为此时通过 npm install 安装时速度会非常慢）**

**方法一：输入以下命令，直接对cnpm的镜像站进行配置。以后在进行插件的安装时还是直接使用 npm install 命令：**
```bash
npm config set registry https://registry.npm.taobao.org
```
![image](Windows中Node.js的安装配置/16.png?v=1&type=image)

**方法二：使用阿里定制的 cnpm 命令行工具代替默认的 npm，输入下面代码进行安装：**
```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
![image](Windows中Node.js的安装配置/17.png?v=1&type=image)

安装 cnpm 后，D:\nodejs\node_global\目录下会增加如下内容，cnpm命令就是在该目录下：

![image](Windows中Node.js的安装配置/18.png?v=1&type=image)

以后安装插件时只需要使用 cnpm intall 命令即可。

但别忘了将 cnpm 的本地仓库和缓存目录也改成我们刚才新建的 D:\nodejs\node_global 和 D:\nodejs\node_cache ：
```bash
cnpm config set prefix "D:\nodejs\node_global"
cnpm config set cache "D:\ndoejs\node_cache"
```
![image](Windows中Node.js的安装配置/19.png?v=1&type=image)

也可以直接打开 cnpm 的配置文件，直接修改其中的内容：

![image](Windows中Node.js的安装配置/20.png?v=1&type=image)

**注意：** 这里运行 cnpm 命令时会出现如下图所示的情况：

![image](Windows中Node.js的安装配置/21.png?v=1&type=image)

**其实在运行 cnpm 命令前要修改环境变量的配置：**

增加环境变量NODE_PATH 内容是：D:\nodejs\node_global\node_modules

![image](Windows中Node.js的安装配置/22.png?v=1&type=image)

然后点击Path进行修改，因为我们有些新的命令在“D:\nodejs\node_global\”目录中（以后通过npm或cnpm安装的插件的命令可能存在于此，包括刚才安装的cnpm的命令也在此），而该目录并没有在环境变量中，所以系统找不到所要执行的命令：

![image](Windows中Node.js的安装配置/23.png?v=1&type=image)

**修改后记得重新打开 命令提示符 执行命令。**

通过以上的 环境配置 后，以后不管是通过 **npm install** 还是 **cnpm install** 来安装我们需要的模块，所安装的模块都会放入“D:\nodejs\node_global\node_modules\”目录中，包括刚才我们安装的 cnpm 也在该目录中，而并没有在“D:\nodejs\node_modules”中。因为我们执行了 npm config set prefix "D:\nodejs\node_global" 和 cnpm config set prefix "D:\nodejs\node_global"，这也是为什么在上面我们说模块目录会改变的原因。

### 三、更新 npm

执行如下命令进行更新（该命令既是安装命令，又是更新命令）：
```bash
cnpm install npm -g
```
或者
```bash
npm install npm -g
```
通过 npm -v 命令查看 npm 的版本信息：
![image](Windows中Node.js的安装配置/24.png?v=1&type=image)

与之前的6.13.4相比，确实更新了。

此时“D:\nodejs\node_global\node_modules\”目录中会存在我们已安装的如下模块：
![image](Windows中Node.js的安装配置/25.png?v=1&type=image)

相当于新更新的 npm 模块也被安装到了该目录下。

至此，恭喜你所有的安装、配置、换源操作都已结束，可以开心的使用 npm 了。
