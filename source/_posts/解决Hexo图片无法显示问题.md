---
title: 解决Hexo图片无法显示问题
date: 2020-05-31 11:59:11
categories: 踩坑记录
tags: [hexo]
---
# 解决Hexo图片无法显示问题
## 问题描述
版本：hexo 3以上
最近搭建hexo博客时遇到了图片部署后不显示的问题，如图：
![问题复现](解决Hexo图片无法显示问题/1.png?v=1&type=image)
上网找了很多方式都没有完美解决问题，后来查看了官方文档后终于解决了问题（完美解决），现在贴出来如下。建议以后大家遇到了问题也先去看看官方文档：[https://hexo.io/zh-cn/docs](https://hexo.io/zh-cn/docs "hexo文档")

解决方案如下：

　　1. 在根目录下配置文件`_config.yml`  中有  `post_asset_folder:false改为true`。这样在建立文件时，`Hexo`会自动建立一个与文章同名的文件夹，这样就可以把与该文章相关的所有资源（图片）都放到那个文件夹里方便后面引用。如这里我放了一张test.jpg的图片。

　　2. git bash安装插件：npm install https://github.com/7ym0n/hexo-asset-image --save（这是个修改过的插件，经测试无问题），使用这个插件来引入图片，而不是网上那些方法里说的用传统md语法相对路径的方法。

　　3. 插入图片时用这种方式：
				 
		 ![图片描述](本地图片测试/test.jpg)
		 
　	其中test.jpg就是你要引用的图片，我这里就是test.jpg，前面的[]里是图片描述，可以自己修改。“本地图片测试”是和发布的博客同名的文件夹。
　	4. 这样就能成功显示了，测试下吧：hexo cl && hexo g && hexo d
