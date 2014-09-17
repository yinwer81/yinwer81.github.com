---
layout: post
title: "Preview github pages"
categories: github-pages
---
#### Windows7环境，经测试实践无错记录。
<br />
本篇主要讲怎样在本地预览github pages，接上篇[blog using github pages](/2014/09/blog-using-github-pages/)

#### 下面是详细步骤：
<br />
安装ruby, python并添加环境变量，解压缩devkit到c:\devkit后，命令行切换到c:\devkit目录

    ruby dk.rb init
    ruby dk.rb install
    gem sources -l
    gem sources --remove http://rubygems.org/
    gem sources -a http://ruby.taobao.org/

    gem list | cut -d" " -f1 | xargs gem uninstall -aIx
    gem install github-pages --no-ri --no-rdoc
    gem update --no-ri --no-rdoc

涉及显示中文等显示问题，需设置LANG和LC_ALL两个环境变量，其值均为zh_CN.UTF-8

在yinwer81.gitub.com目录中执行：jekyll serve --watch，通过http://localhost:4000访问预览

    jekyll serve --watch

Windows7使用[git-credential-winstore](/downloads/git-credential-winstore.exe)记住密码，需注意以下几点：

* 使用https方式clone github的repository
* 将git-credential-winstore.exe放在与git.exe相同位置
* 切换到命令行，执行git-credential-winstore -i git.exe，在弹出框中点Yes安装
* 第一次提交时会弹出Git Credentials Windows安全对话框，输入帐号和密码
* 使用登录https://github.com的帐号和密码，而不是SSH Key
* 密码凭据保存在：控制面板->用户帐号->管理您的凭据->普通凭据(git:https://github.com)

Mac OS X使用~/.netrc记住密码，按以下方式操作：

    vi ~/.netrc
    machine github.com
    login your_account
    password your_password
    :wq<保存退出>

以后再提交时，已无需输入账号和密码。

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

