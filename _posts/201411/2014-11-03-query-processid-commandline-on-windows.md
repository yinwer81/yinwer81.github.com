---
layout: post
title: "Windows下查询ProcessId和CommandLine"
categories: programming
---

 Windows7 64bits环境，使用WMIC查询ProcessId和CommandLine。

以记事本(notepad.exe)为例如下：

	wmic process where "name='notepad.exe'" get ProcessID, Commandline /format:list

图示中当前打开1个记事本ProcessId=11140：
![示例](/images/wmic_notepad.png)


于是包装成批处理文件wmic.bat：

	wmic process where "name='%1'" get ProcessID, Commandline /format:list

使用示例：wmic.bat notepad.exe

以上，有兴趣的可以继续深挖WMIC。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
