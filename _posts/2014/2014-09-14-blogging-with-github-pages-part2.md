---
layout: post
title: "Blogging with github pages part2"
categories: github-pages
---
#### 本篇记录在Windows7 64bits环境下本地预览github pages，接上篇[blogging with github pages part1](/20140912/blogging-with-github-pages-part1/)。
<br />
#### 下面是详细过程：
<br />
首先，安装ruby到C:/Ruby200-x64(全部勾选), python到C:/Python27并配置环境变量，解压缩devkit，启动msys命令行执行下面命令，初始化本地github-pages环境：
    
    cd /c/devkit/
    ruby dk.rb init
按照提示，编辑/c/devkit/config.yml，添加ruby的安装目录`- C:/Ruby200-x64`，接着往下：

    ruby dk.rb install
    gem sources -l

    # 将原Gem源拿掉
    gem sources --remove http://rubygems.org/
    # 换成taobao的Gem源镜像
    gem sources -a http://ruby.taobao.org/

    # 卸载所有gem包，default gem包卸载不掉出错不管
    gem list | cut -d" " -f1 | xargs gem uninstall -aIx

    # 安装github-pages环境所需gem包
    gem install github-pages --no-ri --no-rdoc
    gem update --no-ri --no-rdoc

    # 安装wdm
    gem install wdm --no-ri --no-rdoc

注意：涉及显示中文等显示问题，需设置LANG和LC_ALL两个环境变量，其值均为zh_CN.UTF-8。

然后，在yinwer81.gitub.com目录中执行下面命令后(没有任何错误消息)，通过http://localhost:4000访问预览。

    jekyll serve --watch

下面是执行`jekyll serve --watch`命令启动Jekyll服务器预览时几个常见的错误及解决方法：
>* 报大概下面错误时，修改_config.xml，将highlighter设置为`rouge`，本地预览不报错了，代码高亮也OK，**但是推送到Github仓库后，Rouge会导致编译失败，Github Pages不支持Rouge？请知道的朋友们指教！**

    c:/Ruby200-x64/lib/ruby/gems/2.0.0/gems/posix-spawn-0.3.9/lib/posix/spawn.rb:164: 
    warning: cannot close fd before spawn
    'which' 不是内部或外部命令，也不是可运行的程序或批处理文件。

>* 报大概下面错误时，执行`gem install wdm --no-ri --no-rdoc`安装wdm

    Please add the following to your Gemfile to avoid polling for changes:
      require 'rbconfig'
      if RbConfig::CONFIG['target_os'] =~ /mswin|mingw|cygwin/i
          gem 'wdm', '>= 0.1.0'
      end
按照以上步骤安装github-pages本地预览环境，基本不会有问题，如下所示：
    
    $ jekyll server --watch

    Configuration file: C:/msysgit/yinwer81.github.com/_config.yml
                Source: C:/msysgit/yinwer81.github.com
           Destination: C:/msysgit/yinwer81.github.com/_site
          Generating...
                        done.
     Auto-regeneration: enabled for 'C:/msysgit/yinwer81.github.com'
    Configuration file: C:/msysgit/yinwer81.github.com/_config.yml
        Server address: http://0.0.0.0:4000/
      Server running... press ctrl-c to stop.

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

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

