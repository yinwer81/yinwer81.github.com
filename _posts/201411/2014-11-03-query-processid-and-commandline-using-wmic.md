---
layout: post
title: "Query processId and commandLine using WMIC"
categories: programming
---

 Windows7环境使用WMIC查询ProcessId和CommandLine。

以记事本(notepad.exe)为例如下：

	wmic process where "name='notepad.exe'" get ProcessID, Commandline /format:list

图示中当前打开1个记事本ProcessId=11140：
![示例](/images/wmic_notepad.png)


于是包装成批处理文件wmic.bat：

	wmic process where "name='%1'" get ProcessID, Commandline /format:list

使用示例：wmic.bat notepad.exe

就酱，嗯。有兴趣的可以继续深挖WMIC。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
