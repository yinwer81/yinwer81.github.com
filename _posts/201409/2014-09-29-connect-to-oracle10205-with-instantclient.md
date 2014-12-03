---
layout: post
title: "Connect to Oracle10205 with instantClient"
categories: Oracle
---
[上一篇setup oracle10205 on windows server](/20140928/setup-oracle10205-on-windows-server/)刚说到在Windows服务器上安装Oracle10205，本篇记录免装Oracle客户端，使用非常轻量的sqldbx连接到数据库Oracle10205。

其中所用软件和版本如下，都已搜集到[度盘这里](http://pan.baidu.com/s/1i3tFzal)供参考：
>* instantclient-basic-win32-10.2.0.5.zip
>* sqldbx
>* tnsnames.ora样例文件

#### 下面是详细过程以及出错调试方法：
<br />
首先，解压缩instantclient到C:\instantclient，将sqldbx.exe拷贝到oci.dll相同目录，启动sqldbx。

在sqldbx的Tools->Options->Servers中设置Additional Oracle Home：

	client,C:\instantclient

数据库类型选择Oracle，接着选择服务器，根据错误日志提示将tnsnames.ora拷贝正确目录，并修改tnsnames.ora中HOST/PORT为正确的oracle服务器IP地址和端口。

经测试，以上sqldbx配置可以正常连接到Oracle数据库。

下面记录几个sqldbx常见的出错及解决方法：
>* 怎么看日志？根据sqldbx版本不同，以sqldbx.exe -l启动，会有对应sqldbx.exe.log文件，另外，菜单tools->view application log查看日志信息。

>* 找不到TNSNAMES.ORA，还是看日志，可能需要将TNSNAMES.ORA放在不同目录。

>* 提示不能使用x86 oci.dll？将sqldbx.exe拷贝到oci.dll相同目录执行。

以上，您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
