---
layout: post
title: "Apache Cordova 入门(1)"
categories: Cordova
---

本系列记录下面五方面内容，cordova-tutorial项目代码Host在[Github这里](https://github.com/yinwer81/cordova-tutorial)，感兴趣的可以参考学习。
>* 使用CLI(Command Line Interface)创建编译打包Cordova项目
>* 使用Json, LocalStorage, Memory和Websql进行数据存取
>* 调用Geolocation, Contacts和Camera等Cordova APIs
>* 处理和优化TouchEvents, Scrolling, Styling和PageTransitions等Mobile特性
>* 使用Single-Page Architecture和HTML templates构建应用

本篇记录在Windows7 64bits环境中怎样使用CLI(Command Line Interface)创建编译打包Cordova(Android)项目，iOS环境另篇再讲。

#### 安装配置nodeJS和npm环境
<br/>
下载和安装nodeJS，执行命令`node -v`和`npm -v`打印nodeJS和npm版本来测试和确认：

	$ node -v
		v0.10.33
	$ npm -v
		1.4.28
若报错`Error: ENOENT, stat 'C:\Users\Yinwer\AppData\Roaming\npm'`，则到对应位置手工创建npm文件夹即可。

#### 安装和更新Cordova
<br/>
命令行执行全局安装更新Cordova：
	
	# Windows执行
	$ npm install -g cordova
	$ npm update -g cordova
	
	# Mac和Linux执行
	$ sudo npm install -g cordova
	$ sudo npm update -g cordova

网络速度是天朝行情，幸亏有taobao镜像，使用以下命令配置镜像地址：

	# 指定镜像地址为https://registry.npm.taobao.org，并打印underscore包信息以测试是否生效
	$ npm --registry https://registry.npm.taobao.org info underscore

也可以创建`C:\Users\Yinwer\.npmrc`文件，加入内容`registry=https://registry.npm.taobao.org`以配置镜像。

#### 创建Cordova项目
<br/>
切换到`C:\Users\Yinwer`文件夹，使用以下命令创建workspace-cordova文件夹，并创建HelloCordova程序。

	$ cd C:\Users\Yinwer\workspace-cordova

	$ cordova create HelloCordova com.dunkcoder HelloCordova

Timeout后修改`<npm文件夹>/node_modules/cordova-lib/src/cordova/platforms.js`没有成功，使用命令行代理：

	set http_proxy=http://XXX.XXX.XXX.XXX:XXX
	set http_proxy_user=username
	set http_proxy_pass=password

#### 添加Android平台支持
<br/>
下载并解压缩最新Android SDK，地址在[Android这里](http://developer.android.com/sdk/index.html)，解压缩后配置环境变量：
	
	ANDROID_HOME = C:\adt-bundle-windows-x86_64-20140702\sdk
	将`%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools;`添加到PATH中。

下载并解压缩Ant，地址在[Apache这里](http://ant.apache.org/bindownload.cgi)，解压缩后配置ANT_HOME并将`%ANT_HOME%\bin;`添加到PATH中。

执行以下命令，添加项目对Android平台的支持时，报错`Error: Please install Android target "android-19"`。
	
	$ cd HelloCordova
	$ cordova platforms add android
解决办法：SDK Manager中安装Android 4.4.2(API 19)即可，菜单：Tools->Options中可以设置代理。

接着，执行以下命令安装device和console插件到HelloCordova项目(安装到HelloCordova/plugins目录)：

	$ cordova plugin add org.apache.cordova.device
	$ cordova plugin add org.apache.cordova.console

在SDK Manager中菜单：Tools->Manage AVDs ...创建一个Android模拟器：

![示例](/images/createAVD.png)

编译运行HelloCordova程序，启动Android模拟器，模拟器启动很慢，需要多等一会。

	$ cordova build
	
	# 编译HelloCordova/platforms/android文件夹内的项目，运行到模拟器
	$ cordova emulate android
	
	# 编译HelloCordova/platforms/android文件夹内的项目，运行到连接在电脑USB上的Android设备
	$ cordova run android

编辑www目录下的index.html，修改以下内容：

	<p class="event received">Device is Ready</p>
	-->
	<p class="event received">HelloCordova!</p>
再次启动Android模拟器，确认OK，显示界面部分截图如下所示：

![示例](/images/HelloCordova.png)

下载cordova-tutorial项目代码，解压缩后将starter-www文件夹中内容拷贝到HelloCordova的www文件夹中，再次启动Android模拟器，确认OK，显示界面部分截图如下所示：

![示例](/images/CordovaTutorial.png)

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
