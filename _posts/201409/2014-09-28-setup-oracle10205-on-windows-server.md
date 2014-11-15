---
layout: post
title: "Setup Oracle10205 on Windows server"
categories: Oracle
---
#### 本篇记录在Windows2008R2 64bits环境中安装和配置Oracle10205过程，以及一些注意事项。
<br />
所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1kTMHAOv)：
>* 10204_vista_w2k8_x64_production_db.zip 《Oracle10204安装包》
>* p8202632_10205_MSWIN-x86-64.zip 《Oracle10205安装包》
>* p8350262_10205_Generic.zip 《EM证书过期补丁》

#### 下面开始：
<br />
首先，安装Oracle10204，注意：不要创建启动数据库，否则可能会出错。

Oracle安装程序先决条件检查，参考修改下面两处：

第一处：%DATABASE%\stage\prereq\db\refhost.xml，添加以下内容到适当位置：
{% highlight xml %}
<OPERATING_SYSTEM>
    <VERSION VALUE="6.1"/>
</OPERATING_SYSTEM>
{% endhighlight %}
第二处：%DATABASE%\install\oraparam.ini，增加值项6.1的配置：

    [Certified Versions]
    Windows=5.0,5.1,5.2,6.0,6.1

    #增加以下空Section
    [Windows-6.1-optional]

安装主目录设置为：OraDb10g_home1 = C:\oracle\product\10.2.0\db_1


接着，升级安装Oracle10205，先通过Oracle安装程序先决条件检查，后升级主目录须指定为OraDb10g_home1，同样不要创建启动数据库。


然后，apply补丁p8350262_10205_Generic

打补丁之前，需设置两个环境变量：

    ORACLE_HOME = C:\oracle\product\10.2.0\db_1
	ORACLE_SID = ORCL

接下来，命令行切换到%p8350262_10205_Generic%\8350262\执行以下命令安装EM证书过期补丁：

    %ORACLE_HOME%\OPatch\opatch apply

以上，Oracle10205安装就结束了，下面配置Listener和创建数据库。

首先，使用Net Configuration Assistant创建LISTENER，使用Winzard，不会有什么问题，自不必说。接着，使用DBCA向导创建数据库，使用Unicode(AL32UTF8)字符集存储多语言组，同时设置环境变量NLS_LANG为：

    SELECT USERENV('LANGUAGE') FROM DUAL;


我们可以使用SQLPLUS进行以下确认：

命令行敲入sqlplus，输入帐号和密码，确认数据库环境和版本：

    Connected to:
	Oracle Database 10g Enterprise Edition Release 10.2.0.5.0 - 64bit Production
	With the Partitioning, OLAP, Data Mining and Real Application Testing options

查看当前登录ORACLE_SID：

    SELECT NAME FROM V$DATABASE;

查看当前登录帐号：

    SELECT SYS.LOGIN_USER FROM DUAL;

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
