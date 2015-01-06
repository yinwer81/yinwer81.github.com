---
layout: post
title: "Setup SVN service using subversionedge"
categories: svn
---
#### 本篇记录基于SubversionEdge搭建和配置SVN服务。
<br />
所用软件和版本如下，都已搜集到[度盘这里](http://pan.baidu.com/s/1c0y9A6G)供参考：
>* CollabNetSubversionEdge-4.0.11_linux-x86_64.tar.gz
>* CollabNetSubversionEdge-4.0.11_setup-x86_64.exe
>* VisualSVN.v2.7.3.zip
>* TortoiseSVN-1.7.7.22907-x64-svn-1.7.5.msi

一般来说，搭建SVN版本管理平台可有以下三种选择：
>* Apache + Subversion

支持Windows/Linux等平台；纯手工配置，复杂但足够灵活；可配置支持svn/http/https协议。
>* VisualSVN Server

非常简单，但仅支持Windows，支持http/https协议，但ldap插件需license付费。
>* CollabNet Subversion Edge

支持Windows/Linux等平台；提供WebUI方式配置SVN服务，非常简单却功能强大，支持http/https协议，完全免费和开放源代码，源代码仓库在[这里](https://ctf.open.collab.net/sf/projects/svnedge/)。

<br />
Windows下的安装非常简单，安装Java运行环境并配置环境变量，双击CollabNetSubversionEdge安装包，一路next即可，自不必说。

Linux下的安装稍显麻烦，安装Java运行环境并配置环境变量，接下来参考下面：
>* 在root下创建账号svnadm并设置密码：

    useradd svnadm && passwd svnadm
>* 创建文件夹/subversion4.0.11和/software并配置写权限，上传安装包到/software目录：

    mkdir /subversion4.0.11 && chmod 777 /subversion4.0.11
    mkdir /software && chmod 777 /software

>* 切换到svnadm用户，解压缩安装并启动CSVN：

    cd /software
    tar zxvf CollabNetSubversionEdge-4.0.11_linux-x86_64.tar.gz
    cd csvn/ && mv * /subversion4.0.11 && rm -rf csvn/

    cd /subversion4.0.11
    sudo -E bin/csvn install

    # 查看和编辑 RUN_AS_USER是否设置好
    cat data/conf/csvn.conf

    # 启动svn服务
    bin/csvn start

    # 登陆http://ALIAS.domain.com:3343/csvn创建test仓库
    cd data/repositories && ls -al

    # 查看test仓库的组合用户是否为svnadm:svnadm，否则会用户提交时会报错

打开下面链接（远程登陆需配置防火墙端口），使用初始账号admin/admin登陆后，启动Subversion服务器。

    http://ALIAS.domain.com:3343/csvn

下面记录几个配置实践：

>* 修改后台CSVN端口3343，SVN服务端口和协议等

    修改后台3343端口可以参考csvn-wrapper.conf，SVN服务端口和协议可以在`菜单：管理->Server Settings`配置。

>* 使用HTTPS方式提供SVN服务

菜单：管理->Server Settings->勾选【Subversion服务器应通过https提供服务】，保存即可。
>* 邮件功能配置

菜单：管理->Mail Server，勾选【Enable email notifications】后，配置相关信息即可。
>* 支持ldap和htpasswd双认证

菜单：管理->Authentication，勾选【使用 htpasswd 文件的本地认证以及其它认证方法】和【用 LDAP 服务器进行 LDAP 认证】后，配置相关信息即可。
>* 用户/角色配置

菜单：用户->用户/角色列表，**可惜的是，到目前为止，SubversionEdge做的并不好，或者说根本没有实现。**
>* SVN仓库创建|模板|备份恢复

菜单：管理->Server Settings，可设置版本库（默认%CSVN%\data\repositories）和备份路径（默认%CSVN%\data\dumps）。

菜单：版本库->创建，从SVN版本库模板和从SVN备份文件创建版本库。

菜单：版本库->Manage Templates，系统自带2个模板（Empty repository和标准trunk/branches/tag结构）。

菜单：版本库->Backup Schedule，常用有两种备份方式：Full Dump Backup和Hotcopy Backup。
>* SVN版本库访问权限配置策略，菜单：版本库->访问规则，参考下例：

	[groups]
    g_svnadm = svnadm
    g_test_mgrs = john, mary
	g_test_devs = jim, joe, bill, john, mary
	g_test_docs = sue, john

	[/]
	@g_svnadm = rw

	[test:/]
	@g_test_mgrs = rw

	[test:/tags]
	@g_test_devs =
    @g_test_docs =

	[test:/trunk/docs]
    @g_test_devs =
	@g_test_docs = rw

访问方式及链接：

>* SubversionEdge控制台：

    http://ALIAS.domain.com:3343/csvn/

>* 从SubversionEdge控制台登录后，在管理->Status标签处可以看到相关访问方式，例：

    Subversion 状态: 运行中
	主机名 ALIAS.domain.com
	版本库父文件夹：http://ALIAS.domain.com:8080/svn/
	浏览版本库：http://ALIAS.domain.com:8080/viewvc/

以上，您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
