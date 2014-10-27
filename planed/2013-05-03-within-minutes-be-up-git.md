---
layout: post
title: "分分钟走你Git"
---

搭建内部Git版本管理平台有两种选择：

	Windows Gitblit
	Linux Gitlab

Windows2008 Server R2环境, Gitblit搭建Git Server实践记录。

Gitblit官方网站: [戳这里](http://gitblit.com/), 最新2013-08-22版本1.3.2, GO版本, 需Java运行环境支持。

	解压缩到C:/gitblit-1.3.2, 启动gitblit.cmd, 默认即可通过https://localhost:8443浏览访问
		初始账号密码: admin/admin
	
	自定义配置: C:/gitblit-1.3.2/data/gitblit.properties

	配置项server.contextPath = /gitblit, server.httpPort = 8080, server.httpsPort = 0
		可通过http://localhost:8080/gitblit访问

	配置项git.repositoriesFolder = d:/gitREPOs(默认C:/gitblit-1.3.2/data/git)
		Git Repositories目录为d:/gitREPOs
	
	installService.cmd/uninstallService.cmd, 可安装/卸载windows gitblit服务

	SSL证书: 双击启动authority.cmd, 为每个用户颁发证书并导入
		gitblit keystore密码为server.storePassword配置项值，默认为gitblit

	创建git库: test, 尝试进行clone, push, pull等操作, 注意: 中文有问题!!
		修改浏览器语言设置为英文应可解决.

	以上即可把gitblit搭建的Git Server使用起来, 更多可参考官方网站

	优点: GO/WAR等多版本可用;通过Manager可远程管理Gitblit server;federation备份策略等
	缺点: 版本更新慢;尚不支持PR等;用户较少等

Gitlab, X64 Ubuntu 10.04 LTS环境, 实践记录

官方网站: [戳这里](http://gitlab.org/), 最新版本6.1.0-0, 下载使用BitNami Gitlab Stack: [戳这里](http://bitnami.com/stack/gitlab/installer)

	chmod +755 bitnami-gitlab-6.1.0-0-linux-x64-installer.run
	./bitnami-gitlab-6.1.0-0-linux-x64-installer.run 启动安装

	邮件设置(exchange server)
		gitlab.yml: 修改配置项email_from和support_email
		production.rb:
			  	config.action_mailer.perform_deliveries = true
  			  	config.action_mailer.raise_delivery_errors = true
				config.action_mailer.delivery_method = :smtp
  				config.action_mailer.smtp_settings = {
    				:address => mail_address,
    				:port => "25",
    				:domain => mail_domain,
    				:authentication => :login,
    				:user_name => mail_account,
    				:password => mail_password,
    				:enable_starttls_auto => false
  				}

以上, 后边将陆续更新分享gitblit和gitlab(CI)实践。