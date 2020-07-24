---
title: python安装第三方库的方法汇总
author: Mr.Yang
date: 2020-07-24 20:33:33
categories: 备忘
tags: [python,环境配置]
---
## 引言：
最近因为项目的问题，我在配置python环境的过程中需要安装一些第三方库，下面我将学习过程中总结和搜集的一些安装方法进行汇总，方便自己日后查看和交流学习。
### 方法一：pip命令行直接安装（最简易可行）
默认已将安装好python，并进行了相关环境的配置。接下来，**win + r**打开命令窗口，输入命令 **pip install 包名**，回车即可安装第三方库。但是，由于国内网络原因，某些第三方库可能下载的速度很慢，可能导致安装失败。这里我推荐在使用**pip**安装的时候，切换成国内的一些镜像源。具体的命令为：**pip install 包名 -i 镜像源的地址**。

下面我列举一些国内较常用的镜像源：

清华：```https://pypi.tuna.tsinghua.edu.cn/simple```

阿里云：```http://mirrors.aliyun.com/pypi/simple/```

中国科技大学: ```https://pypi.mirrors.ustc.edu.cn/simple/```

华中理工大学：```http://pypi.hustunique.com/```

山东理工大学：```http://pypi.sdutlinux.org/```

豆瓣：```http://pypi.douban.com/simple/```

如果需要下载到指定环境下，例如我想要安装到`MyProject`中，找到它的路径下的`python.exe`，复制这个可执行文件的绝对路径 `D:\Anaconda3\envs\MyProject\python.exe`
命令为：
**```D:\Anaconda3\envs\MyProject\python.exe pip install 包名 -i 镜像源的地址 ```**

### 方法二：Anaconda安装
默认安装了Anaconda。打开Anaconda，按照下图进行操作就可以了。
![image](python安装第三方库的方法汇总/1.png?v=1&type=image)

### 方法三：利用PyCharm安装
默认安装了PyCharm。打开PyCharm，按照下图进行操作就可以了。
![image](python安装第三方库的方法汇总/2.png?v=1&type=image)
![image](python安装第三方库的方法汇总/3.png?v=1&type=image)
![image](python安装第三方库的方法汇总/4.png?v=1&type=image)
最后点击左下角的install package，等待安装完成即可。
