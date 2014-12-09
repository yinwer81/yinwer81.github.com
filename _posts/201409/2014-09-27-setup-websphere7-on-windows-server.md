---
layout: post
title: "Setup Websphere ND7 on Windows server"
categories: websphere
---
#### 本篇记录在Windows2008R2 64bits环境中安装配置Websphere ND7和patch补丁。
<br />
所用软件版本如下，都已搜集到[度盘这里](http://pan.baidu.com/s/1hqsv5xM)供参考：
>* C1G2JML.zip，《Webshere ND7安装包》
>* 7.0.0.27-WS-UPDI-WinAMD64.zip，《UpdateInstaller打补丁程序》
>* 7.0.0-WS-WAS-WinX64-FP0000027.pak，《WAS 27号补丁》
>* 7.0.0-WS-WASSDK-WinX64-FP0000027.pak，《WASSDK 27号补丁》

#### 下面开始：
<br />
安装Websphere ND7：

* 解压缩C1G2JML.zip，启动launchpad.exe进行安装；
* 建议安装两个可选软件包：管理控制台的非英语语言软件包和应用程序服务器运行时环境的非英语语言软件包；
* 安装目录：C:\IBM\WebSphere\AppServer；
* Websphere Application Server环境，建议选择：无，待安装好WAS7和补丁后，由概要管理工具创建。

安装UpdateInstaller：

* 解压缩7.0.0.27-WS-UPDI-WinAMD64.zip, 启动UpdateInstaller\install.exe进行安装；
* 安装目录: C:\IBM\WebSphere\UpdateInstaller

使用安装好的UpdateInstaller打27号补丁（以27号补丁为例）：

* 输入要更新的产品的安装位置：C:\IBM\WebSphere\AppServer；
* 将27号补丁（两个文件）拷贝到C:\IBM\WebSphere\UpdateInstaller\maintenance目录：


使用概要管理工具(Profile Management Tool)创建应用程序服务器AppSrv01。

登陆集成方案控制台，验证Websphere Application Server套件版本：7.0.0.27

从[ftp.boulder.ibm.com这里](ftp://ftp.boulder.ibm.com/software/websphere/appserv/support/fixpacks)可下载一些patch补丁，可能需要备梯。

本篇就酱，后面将再陆续记录一些Websphere的应用实践。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
