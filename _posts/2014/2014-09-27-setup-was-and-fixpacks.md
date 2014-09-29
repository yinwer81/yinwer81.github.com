---
layout: post
title: "安装Websphere7"
categories: websphere
---
#### Windows2008R2环境，经测试实践无错记录。
<br />
本文主要记录安装Websphere ND7和补丁27，其中所用软件版本如下，都已搜集到[这里](http://pan.baidu.com/s/1hqsv5xM)供下载。
>* C1G2JML.zip
>* 7.0.0.27-WS-UPDI-WinAMD64.zip
>* 7.0.0-WS-WAS-WinX64-FP0000027.pak
>* 7.0.0-WS-WASSDK-WinX64-FP0000027.pak

#### 下面是安装过程和一些注意事项：
<br />
安装Websphere ND7：

* 解压缩C1G2JML.zip，启动launchpad.exe进行安装；
* 建议安装两个可选软件包：
> 管理控制台的非英语语言软件包<br />
> 应用程序服务器运行时环境的非英语语言软件包
* 安装目录：C:\IBM\WebSphere\AppServer；
* Websphere Application Server环境，建议选择：无，待安装好WAS7和补丁后，由概要管理工具创建。

安装UpdateInstaller：

* 解压缩7.0.0.27-WS-UPDI-WinAMD64.zip, 启动UpdateInstaller\install.exe进行安装；
* 安装目录: C:\IBM\WebSphere\UpdateInstaller

安装升级补丁：

* 输入要更新的产品的安装位置：C:\IBM\WebSphere\AppServer；
* 安装维护软件包，拷贝到C:\IBM\WebSphere\UpdateInstaller\maintenance目录：
> 7.0.0-WS-WAS-WinX64-FP0000027.pak<br />
> 7.0.0-WS-WASSDK-WinX64-FP0000027.pak

使用概要管理工具(Profile Management Tool)创建应用程序服务器AppSrv01：

* WASND7版本支持五种特定类型的环境:
> 单元(Deployment Manager和联合应用程序服务器)<br />
> 管理<br />
> 应用程序服务器<br />
> 定制概要文件<br />
> 安全代理(仅用于配置)
* 创建应用程序服务器AppSrv01
> 概要管理工具->应用程序服务器->典型概要文件->启用管理安全性->...

安装验证|启动服务器|管理控制台

* 使用【启用管理安全性】处设置的账号和密码，登陆集成方案控制台
> 链接大约是：https://${DOMAIN}:9043/ibm/console/logon.jsp)
* 验证Websphere Application Server套件版本：7.0.0.27

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

