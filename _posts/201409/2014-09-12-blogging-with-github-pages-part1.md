---
layout: post
title: "Blogging with github pages part1"
categories: github-pages
---
#### 本篇记录在Windows7 64bits环境下连接Github，Clone和提交Github-Pages博客仓库。
<br />
所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1o6Nrahc)：
>* msysGit-fullinstall-1.8.1.2-preview20130201.exe
>* rubyinstaller-2.0.0-p576-x64.exe
>* python-2.7.3.msi
>* DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe
>* git-credential-winstore.exe

#### 下面以本博客为例：
<br />
注册Github帐号：yinwer81，通过邮件认证后，创建名称为yinwer81.github.com(须全称)的仓库，然后将空仓库Clone到本地：

	$ git clone https://github.com/yinwer81/yinwer81.github.com.git

下载msysgit程序包并安装，比如安装在C:\msysgit，然后使用msys.bat切换到仓库目录设置以下信息：

	$ cd yinwer81.github.com/
	$ git config user.name "yinwer81"
	$ git config user.email "yinwer81@gmail.com"

Clone喜欢的博客模板，我对前端不擅长，且非常喜欢这个[博客模板](http://blog.sevenche.com/)，非常感谢Jim的辛勤劳动。

	$ git clone https://github.com/dashjim/dashjim.github.com.git

拷贝dashjim.github.com文件夹中所有内容(.git目录除外)到yinwer81.github.com文件夹中，删除Jim的原有博文，把相关配置信息改成自己的，然后提交到github：

	$ git add .
	$ git commit -m "initial commit"
	$ git push origin master

几分钟后浏览http://yinwer81.github.com，**若发布不成功，邮件或者仓库的Setting页面（右边Setting链接）Github Pages段落会提示失败**，同时要注意是否有CNAME设置。

如何在本地调试预览github pages，请参考下篇[blogging with github pages part2](/20140914/blogging-with-github-pages-part2/)

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

