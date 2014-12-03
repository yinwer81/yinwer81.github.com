---
layout: post
title: "Setup and config redmine service"
categories: redmine
---

#### 本篇记录在Ubuntu 64bits环境下搭建和配置Redmine。
<br />
所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1gdzPxwJ)供参考：
>* bitnami-redmine-2.3.0-2-linux-installer.run
>* bitnami-redmine-2.3.0-2-windows-installer.exe

首先，Redmine版本1.x和2.x区别，可以参考[Redmine News](http://www.redmine.org/news/66)：
>* Redmine 1.x分支基于Rails2.3版本开发
>* 从2.0.0开始升级到Rails3，且Rail core team不再维护原Rails2.3
虽然升级到Redmine2.x，功能和性能都会有些改进，但Redmine的很多插件并不兼容，故建议审慎考虑使用Redmine哪个版本。

使用Bitnami Redmine All-in-One安装包，非常简单，一路next，设置管理员信息和账号密码等，自不必说。

#### 下面记录几个配置实践：
<br />
可以使用mysql客户端工具连接bitnami_redmine数据库：账号root，密码为redmine登陆密码。

配置发送邮件功能SMTP，适当修改文件configuration.yml以下内容：

	REDMINE安装目录\apps\redmine\htdocs\config\configuration.yml

	default:
	  email_delivery:
	    delivery_method: :smtp
	    smtp_settings:
	      tls: false
		  address: <smtp地址:smtp.email.com>
		  port: 25 <smtp端口>
		  domain: <domain:email.com>
		  authentication: :login
		  user_name: "account@email.com"
		  password: "account_password"

测试发送邮件功能SMTP，使用管理员登录后，在菜单：管理->配置->邮件通知页面，点击发送测试邮件链接。

配置使用HTTPS协议的SVN版本库

>* Windows2008R2环境成功例(假定Redmine安装在C:\redmine-2.3.0-2目录)

    切换到REDMINE安装目录，执行命令(https svn中的test仓库)：

	cd  C:\redmine-2.3.0-2\subversion\bin\
	svn ls --config-option config:auth:store-auth-creds=yes --config-dir ssldir https://IP/svn/test

	修改subversion_adapter.rb中credentials_string方法第四行为：
	str << " --no-auth-cache --non-interactive --config-dir C:\\redmine-2.3.0-2\\subversion\\bin\\ssldir"

    重启Redmine

>* Ubuntu 64bits环境成功例(假定Redmine安装在/opt/redmine-2.3.0-2目录)

    切换到REDMINE安装目录，执行命令(https svn中的test仓库)：

	cd /opt/redmine-2.3.0-2/subversion/bin
    svn ls --config-option config:auth:store-auth-creds=yes --config-dir ssldir https://IP/svn/test

    <使apache所在用户和组成为ssldir目录owner>
	ps -ef | grep apache
	ls -al
	sudo chown -R daemon.daemon /opt/redmine-2.3.0-2/subversion/bin/ssldir/

    修改subversion_adapter.rb中credentials_string方法第四行为：
	str << " --no-auth-cache --non-interactive --config-dir /opt/redmine-2.3.0-2/subversion/bin/ssldir"

    重启Redmine

测试使用HTTPS协议的SVN，在菜单：test项目->配置->版本库，新建https的版本库，看是否可以正常拉取版本库内容。

配置使用GIT版本库：

>* Redmine目前仅支持通过文件系统存取本机git仓库，故只能创建计划任务定期获取git仓库最新内容：

    #添加git到PATH环境变量
	PATH=/opt/redmine-2.3.0-2/git/bin:$PATH
	echo $PATH | grep git

    #拉取test项目仓库到本机路径：/gitRepos/test
	cd /gitRepos
	git clone test项目git仓库路径

	Redmine->test项目->配置->版本库中选择配置GIT，库路径：/gitRepos/test

	新增linux计划任务使得Redmine可以自动获取test项目git仓库更新
	vi /gitRepos/redmine_autopull.sh
	cd /gitRepos/test/ && git pull origin master
	:wq<保存退出>
	chmod 744 /gitRepos/redmine_autopull.sh

	测试手动执行/gitRepos/redmine_autopull.sh, OK!

	crontab -e
	* * * * * /gitRepos/redmine_autopull.sh

后面将会继续更新一些插件的安装和使用方法，更多内容可以参考[Bitnami Redmine WIKI](http://wiki.bitnami.com/Applications/BitNami_Redmine)。

后面将陆续记录一些Redmine及其插件应用实践。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
