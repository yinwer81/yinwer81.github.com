---
layout: post
title: "免装客户端连接到Oracle10205"
categories: Oracle
---
#### 免装Oracle客户端，使用sqldbx/plsql developer连接到数据库Oracle10205。
<br />
本篇所用软件版本如下，都已搜集到[这里](http://pan.baidu.com/s/1o6Nrahc)供下载。
>* instantclient-basic-win32-10.2.0.5.zip # 助32位instantclient连接到64位oracle
>* sqldbx # 个人版本, 免费但不支持Unicode; 专业版本, 收费支持Unicode
>* plsql developer

#### 下面记录详细配置信息: <p>
1.  本地连接(sqldbx和plsql developer)

		解压缩instantclient到C:\oracle\instantclient_10_2
		创建目录并复制C:\oracle\instantclient_10_2\network\ADMIN\tnsnames.ora

		sqldbx中:
		Tools->Options->Servers中设置Additional Oracle Home：
			client,C:\oracle\instantclient_10_2

		plsql developer中:
		Tools->Preferences->Oracle Connection中设置：
			Oralce Home：C:\oracle\instantclient_10_2
			OCI library：C:\oracle\instantclient_10_2\oci.dll

		测试可以正常连接
2.  免安装ORACLE客户端远程连接(sqldbx和plsql developer)

		解压缩instantclient到C:\instantclient_10_2
		设置环境变量ORACLE_HOME = C:\instantclient_10_2

		创建目录并复制C:\instantclient_10_2\network\ADMIN\tnsnames.ora
		修改tnsnames.ora中HOST/PORT为正确的oracle服务器IP地址和端口

		设置环境变量: NLS_LANG = AMERICAN_AMERICA.AL32UTF8

		sqldbx中:
		Tools->Options->Servers中设置Additional Oracle Home:
			client,C:\instantclient_10_2

		plsql developer中:
		Tools->Preferences->Oracle Connection中设置:
			Oralce Home: C:\instantclient_10_2
			OCI library: C:\instantclient_10_2\oci.dll

		测试可以正常连接
3.  补充几个sqldbx的出错及解决方法(by 2014/06/15)

		报错了, 怎么办? ==> 看日志
			以sqldbx.exe -l启动, 可能会有对应sqldbx.exe.log日志, 查看日志信息
			菜单tools->view application log查看日志信息

			以上两种方式, 据sqldbx个人/专业版本以及不同版本号可能会不同

		找不到TNSNAMES.ORA ==> 怎么办?
			设置环境变量ORACLE_HOME = C:\instantclient_10_2

			还是找不到?
			看日志:
				C:\network\ADMIN\tnsnames.ora
				C:\net80\ADMIN\tnsnames.ora

		提示不能使用x86 oci.dll ==> 怎么办?
			将sqldbx.exe拷贝到oci.dll相同目录执行

		以上几种方式, 经测试可行。

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
