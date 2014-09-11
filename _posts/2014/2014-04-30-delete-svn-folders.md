---
layout: post
title: Delete SVN Folders
---

将svn文件夹变成普通文件夹, 即将文件夹下的.svn递归删除即可。

Windows环境, 拷贝以下脚本, 保存为deletesvn.reg, 亦可点[这里]()下载, 双击导入注册表。</br>
注意: 请使用ANSI编码, 尝试使用utf-8保存, 结果导入注册表失败。
{% highlight java %}
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN] 
@="Delete SVN Folders"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Folder\shell\DeleteSVN\command] 
@="cmd.exe /c \"TITLE Removing SVN Folders in %1 && FOR /r \"%1\" %%f IN (.svn) DO RD /s /q \"%%f\" \""
{% endhighlight %}

使用方式: 在svn文件夹上点右键, Delete SVN Folders, 将弹出一个命令行窗口:</br>
窗口标题: Removing SVN Folders in %文件夹根全路径%</br>
窗口命令: RD /s /q "%文件夹递归全路径%\\.svn"</br>

Linux环境, 在该目录下执行下面两个命令都是可以的:
{% highlight java %}
find . -name "*.svn" -print -exec rm -rf  {} \;
find . -name "*.svn" | xargs rm -rf
{% endhighlight %}

以上。