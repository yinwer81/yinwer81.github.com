---
layout: post
title: "免装客户端连接到Oracle10205"
categories: Oracle
---
#### 本篇记录免装Oracle客户端，使用sqldbx连接到数据库Oracle10205。
<br />
其中所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1i3tFzal)。
>* instantclient-basic-win32-10.2.0.5.zip
>* sqldbx
>* tnsnames.ora样例文件

#### 下面记开始：
<br />
首先，解压缩instantclient到C:\instantclient，将sqldbx.exe拷贝到oci.dll相同目录，启动sqldbx。

在sqldbx的Tools->Options->Servers中设置Additional Oracle Home（sqldbx版本不同，登陆界面会有Oracle_HOME选择）：

	client,C:\instantclient

数据库类型选择Oracle，接着选择服务器，根据错误日志提示将tnsnames.ora拷贝正确目录，并修改tnsnames.ora中HOST/PORT为正确的oracle服务器IP地址和端口。

经测试，以上sqldbx配置可以正常连接到Oracle数据库。

下面记录几个sqldbx常见的出错及解决方法：
>* 怎么看日志？根据sqldbx版本不同，以sqldbx.exe -l启动，会有对应sqldbx.exe.log文件，另外，菜单tools->view application log查看日志信息。

>* 找不到TNSNAMES.ORA，还是看日志，可能需要将TNSNAMES.ORA放在不同目录。

>* 提示不能使用x86 oci.dll？将sqldbx.exe拷贝到oci.dll相同目录执行。

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
