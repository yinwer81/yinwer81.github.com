---
layout: post
title: "ps -ef | grep xxx on Windows"
---

Windows ps -ef | grep xxx, Windows7环境, 实践无错记录。

举例如下: <br>
{% highlight java %}
wmic process where "name='notepad.exe'" get ProcessID, Commandline /format:list
{% endhighlight %}
![示例](/images/psefgrepxxx.png)

图示说明: 当前打开2个记事本ProcessId=5244和ProcessId=7712<br>

包装成批处理文件wmic.bat: <br>
{% highlight java %}
wmic process where "name='%1'" get ProcessID, Commandline /format:list
{% endhighlight %}
使用示例: {% highlight java %}wmic.bat notepad.exe{% endhighlight %}

有兴趣的可以继续深挖WMIC。
