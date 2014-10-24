---
layout: post
title: "Blogging with github pages part3"
categories: github-pages
---
#### 本篇记录在Mac Yosemite 64bits环境下本地预览github pages。
<br />

确认本机ruby/git环境(Yosemite自带)，在iTerm2中敲入：

	Yinwer$ ruby -v

	ruby 2.0.0p481 (2014-05-08 revision 45883) [universal.x86_64-darwin14]

	Yinwer$ git --version

	git version 1.9.3 (Apple Git-50)

接下来，使用git命令从github将仓库Clone下来：

	Yinwer$ git clone https://github.com/yinwer81/yinwer81.github.com.git

iTerm2中切换到`yinwer81.github.com`目录，执行以下命令初始化github-pages环境：
	
	Yinwer$ gem sources -l

    # 将原Gem源拿掉
    Yinwer$ gem sources --remove https://rubygems.org/
    # 换成taobao的Gem源镜像
    Yinwer$ gem sources -a http://ruby.taobao.org/

    # 安装github-pages环境所需gem包
    Yinwer$ sudo gem install github-pages --no-ri --no-rdoc
    Yinwer$ sudo gem update --no-ri --no-rdoc

    # 安装rouge
    Yinwer$ sudo gem install rouge --no-ri --no-rdoc

按照以上步骤安装github-pages本地预览环境，基本不会有问题，如下所示：
    
    Yinwer$ jekyll server --watch

    Configuration file: /Users/Yinwer/yinwer81.github.com/_config.yml
                Source: /Users/Yinwer/yinwer81.github.com
           Destination: /Users/Yinwer/yinwer81.github.com/_site
          Generating...
                        done.
     Auto-regeneration: enabled for '/Users/Yinwer/yinwer81.github.com'
    Configuration file: /Users/Yinwer/yinwer81.github.com/_config.yml
        Server address: http://0.0.0.0:4000/
      Server running... press ctrl-c to stop.

Mac OS X使用~/.netrc记住密码：

    vi ~/.netrc
        machine github.com
        login your_account
        password your_password
    :wq

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

