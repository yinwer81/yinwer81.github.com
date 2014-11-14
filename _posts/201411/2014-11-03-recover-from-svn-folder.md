---
layout: post
title: 删除隐藏svn文件夹
categories: svn
---

客户项目Delivery时，我们需要将最新代码一并打包交付，这时需要删除所有的隐藏`.svn`文件夹。

Windows7 64bits环境，有两种方法：

第一种：拷贝以下注册表，另保存为`Delete_SVN.reg`(ANSI编码)，双击导入注册表。

	Windows Registry Editor Version 5.00

	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN] 
	@="Delete SVN Folders"

	[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN\command] 
	@="cmd.exe /c \"TITLE Removing SVN Folders in %1 && FOR /r \"%1\" %%f IN (.svn) DO RD /s /q \"%%f\" \""


在svn文件夹上点右键，选择Delete SVN Folders，将弹出命令行窗口递归删除`.svn`文件夹。

第二种：将以下批处理命令包装成`Delete_SVN_Folders.bat`，拷贝`Delete_SVN_Folders.bat`到SVN仓库根目录，双击执行即可。

	for /f "tokens=* delims=" %%i in ('dir /s /b /a:d *.svn') do (rd /s /q "%%i")

两种方法均测试可用。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
