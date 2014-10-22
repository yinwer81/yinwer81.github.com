---
layout: post
title: "Blogging with github pages part2"
categories: github-pages
---
#### 本篇记录在Windows7环境下本地预览github pages，接上篇[blogging with github pages part1](/2014/09/blogging-with-github-pages-part1/)。
<br />
#### 下面是详细过程：
<br />
首先，安装ruby, python并配置环境变量，解压缩devkit，启动msys命令行执行下面命令，初始化本地github-pages环境：
    
    cd /c/devkit/
    ruby dk.rb init
    ruby dk.rb install
    gem sources -l
    gem sources --remove http://rubygems.org/
    gem sources -a http://ruby.taobao.org/

    gem list | cut -d" " -f1 | xargs gem uninstall -aIx
    gem install github-pages --no-ri --no-rdoc
    gem update --no-ri --no-rdoc

注意：涉及显示中文等显示问题，需设置LANG和LC_ALL两个环境变量，其值均为zh_CN.UTF-8。

然后，在yinwer81.gitub.com目录中执行下面命令后，通过http://localhost:4000访问预览。

    jekyll serve --watch

#### 记住密码：
<br />
Windows7使用git-credential-winstore记住密码：

* 将git-credential-winstore.exe放在与git.exe相同位置
* 切换到命令行，执行git-credential-winstore -i git.exe，在弹出框中点Yes安装
* 第一次提交时会弹出Git Credentials Windows安全对话框，输入帐号和密码
* 使用登录https://github.com的帐号和密码，而不是SSH Key
* 以后再提交时，已无需输入账号和密码
* 使用https方式clone github的仓库
* 密码凭据保存在：控制面板->用户帐号->管理您的凭据->普通凭据(git:https://github.com)

Mac OS X使用~/.netrc记住密码：

    vi ~/.netrc
        machine github.com
        login your_account
        password your_password
    :wq

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

