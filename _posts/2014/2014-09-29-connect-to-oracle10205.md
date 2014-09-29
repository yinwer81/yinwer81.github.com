---
layout: post
title: "免装客户端连接到Oracle10205"
categories: Oracle
---
#### 本篇记录免装Oracle客户端，使用sqldbx/plsql developer连接到数据库Oracle10205。
<br />
其中所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1i3tFzal)供下载。
>* instantclient-basic-win32-10.2.0.5.zip
>* sqldbx
>* plsql developer
>* tnsnames.ora样例

#### 下面记录详细配置过程和几个sqldbx常见的出错和调错方案。
<br />
本地和远程连接(sqldbx和plsql developer)：
>* 解压缩instantclient到C:\instantclient
>* 创建目录并复制C:\instantclient\network\ADMIN\tnsnames.ora
>* 修改tnsnames.ora中HOST/PORT为正确的oracle服务器IP地址和端口

在sqldbx的Tools->Options->Servers中设置Additional Oracle Home：

	client,C:\instantclient

在plsql developer的Tools->Preferences->Oracle Connection中设置：

	Oralce Home：C:\instantclient
	OCI library：C:\instantclient\oci.dll

经测试，以上sqldbx/plsql developer配置可以正常连接到Oracle数据库。

几个sqldbx常见的出错及解决方法：
>* 看日志，以sqldbx.exe -l启动，可能会有对应sqldbx.exe.log日志；菜单tools->view application log查看日志信息。

>* 找不到TNSNAMES.ORA，还是看日志，可能需要将TNSNAMES.ORA放在不同目录。

>* 提示不能使用x86 oci.dll？将sqldbx.exe拷贝到oci.dll相同目录执行。

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
