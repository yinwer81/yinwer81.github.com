---
layout: post
title: "Blog using github pages"
---
#### Windows7环境, 经测试实践无错记录。
<br />
本篇主要讲怎样将github pages blog搭建起来, 其中所用软件版本如下, 都已搜集到[这里](http://pan.baidu.com/s/1o6Nrahc)分享供下载。
>* msysGit-fullinstall-1.8.1.2-preview20130201.exe
>* rubyinstaller-1.9.3-p392.exe
>* python-2.7.3.msi
>* DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe
>* git-credential-winstore.exe

#### 下面是详细步骤:
<br />
注册Github帐号: yinwer81, 创建名称为yinwer81.github.com(必须要全称)的Repository

下载并安装msysgit, 比如安装在C:\msysgit, 使用msys.bat生成SSH Key

	//检查是否存在.ssh目录, 如存在则备份rsa密钥
	$ cd ~/.ssh
	$ ls
		config  id_rsa  id_rsa.pub  known_hosts
	$ mkdir backup
	$ cp id_rsa* backup
	$ rm id_rsa*

	//产生新的rsa密钥
	$ ssh-keygen -t rsa -C "yinwer81@gmail.com"
		Generating public/private rsa key pair.
		Enter file in which to save the key (~/.ssh/id_rsa):
		Enter passphrase (empty for no passphrase):
		Enter same passphrase again:
将id_rsa.pub文件中的内容添加到Github -> Account Settings -> SSH Keys项

测试连接github.com情况:

	$ ssh -T git@github.com
		The authenticity of host 'github.com (207.97.227.239)' can't be established.
		RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
		Are you sure you want to continue connecting (yes/no)?<yes>

	Hi user!
	You've successfully authenticated, but GitHub does not provide shell access.

设置全局信息

	$ git config --global user.name "yinwer81"
	$ git config --global user.email "yinwer81@gmail.com"

Clone并提交到REPO

	$ git clone https://github.com/yinwer81/yinwer81.github.com.git
	$ git clone https://github.com/plusjade/jekyll-bootstrap.git

拷贝jekyll-bootstrap所有内容(隐藏.git目录除外)到yinwer81.github.com文件夹中, 并提交到github:

	$ git add .
	$ git commit -m "initial commit"
	$ git push origin master<输入rsa密钥>

几分钟后浏览http://yinwer81.github.com, Yes, it works!

如何在本地调试预览github pages, 请看下篇[github pages本地预览](/2014/09/preview-github-pages/)

以上。您有任何问题或建议, 请给我写[邮件](mailto:yinwer81@gmail.com)。

