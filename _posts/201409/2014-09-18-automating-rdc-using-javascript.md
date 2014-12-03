---
layout: post
title: "Automating RDC using javascript"
categories: programming
---
#### 背景：
<br />
我们有一个webapp，用户在上面可以提交访问指定服务器的请求，审批时webapp会调用后台脚本在服务器上创建账号密码，并发送给用户，用户在一个时间窗口内可以通过Remote的方式访问服务器做程序维护工作。

#### 原做法：
<br />
用户手工方式远程登录，缺点是麻烦易错，用户可以接受但抱怨不断，用户体验不好。

#### 新做法：
<br />
根据服务器IP地址和账号密码生成链接，用户点击链接自动Remote并登陆到服务器，缺点是涉及ActiveX，故仅支持IE浏览器，但避免了用户手工的麻烦和出错，用户点赞！

技术方案是将下面代码包装成mstsc.bat：
{% highlight bash %}
cmdkey /delete:TERMSRV/%1
cmdkey /generic:TERMSRV/%1 /user:%3\\%4 /pass:%5
start mstsc /v:%1:%2
{% endhighlight %}
调用方式：mstsc.bat ip, port, host, user, pass，经测试可行。

OK，用JavaScript将脚本包装起来如下：
{% highlight javascript %}
String.format = function() {
   if (arguments.length == 0)
      return null;
   var str = arguments[0];
   for (var i = 1; i < arguments.length; i++) {
      var reg = new RegExp('\\{' + (i - 1) + '\\}', 'gm');
      str = str.replace(reg, arguments[i]);
   }
   return str;
};

function lunchRdp(ip, port, host, user, pass) {
   var atxObj = null;
   if (window.ActiveXObject) {
      try {
         atxObj = new ActiveXObject("WScript.Shell");
         // command template
         var deleteTempl = "cmdkey /delete:TERMSRV/{0}";
         atxObj.Run(deleteTempl.format(ip));
         var genericTempl = "cmdkey /generic:TERMSRV/{0} /user:{1}\\{2} /pass:{3}";
         atxObj.Run(genericTempl.format(ip, host, user, pass));
         var mstscTempl = "mstsc /v:{0}:{1}";
         atxObj.Run(mstscTempl.format(ip, port));
      } catch (e) {
         alert("Runtime exception occurred, please retry or contact sysadmin.");
      }
   } else {
      alert("IE/IE-embedded browser for this function only.");
   }
};
{% endhighlight %}

以上，IE8测试通过。

您有任何问题或建议, 请给我写[邮件](mailto:yinwer81@gmail.com)。
