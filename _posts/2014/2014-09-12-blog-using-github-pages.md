---
layout: post
title: "Blog using github pages"
categories: github-pages
---
#### Windows7环境，经测试实践无错记录。
<br />
本篇主要讲怎样将github pages blog搭建起来，其中所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1o6Nrahc)供下载。
>* msysGit-fullinstall-1.8.1.2-preview20130201.exe
>* rubyinstaller-1.9.3-p392.exe
>* python-2.7.3.msi
>* DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe
>* git-credential-winstore.exe

#### 下面是具体详细过程：
<br />
注册Github帐号：yinwer81，通过邮件认证，创建名称为yinwer81.github.com(必须要全称)的Repository。

Clone空仓库到本地：

	$ git clone https://github.com/yinwer81/yinwer81.github.com.git

下载并安装msysgit，比如安装在C:\msysgit，使用msys.bat切换到仓库目录设置以下信息：

	$ cd yinwer81.github.com/
	$ git config user.name "yinwer81"
	$ git config user.email "yinwer81@gmail.com"

Clone喜欢的博客模板到本地。我对前端不擅长，且比较喜欢这个[博客模板](http://blog.sevenche.com/)，非常感谢Jim的辛勤劳动。

	$ git clone https://github.com/dashjim/dashjim.github.com.git

拷贝dashjim.github.com文件夹中所有内容(.git目录除外)到yinwer81.github.com文件夹中，修改相关配置信息，然后提交到github：

	$ git add .
	$ git commit -m "initial commit"
	$ git push origin master

几分钟后浏览http://yinwer81.github.com，若发布不成功，邮件或者仓库的Setting页面（右边Setting链接）Github Pages段落会有错误提示，同时要注意是否有CNAME设置。

如何在本地调试预览github pages，请看下篇[github pages本地预览](/2014/09/preview-github-pages/)

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

