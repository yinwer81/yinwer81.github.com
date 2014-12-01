---
layout: post
title: "Frontend tool: Viewreport Resizer"
categories: frontend
---

我们在`Employee Directory`和各个`Public Account`项目中需要测试页面在各种屏幕尺寸设备的展现情况，如：iPad, iPhone, Note等，以前的做法是手工将Chrome窗口调整到分辨率大小，土到掉渣。

在网上瞎转的时候找到了Viewreport Resizer，官网在[这里](http://lab.maltewassermann.com/viewport-resizer/)，同样是基于bookmarklet，下图所示为官网在iPhone5大小竖屏中的页面表现。 
![示例](/images/viewreportresizer.png)

拖拽图中`Click or Bookmark`到书签栏可创建Resizer书签。待页面加载完成以后，点击该标签，将重新加载页面并在顶部显示`Viewport Resizer`Toolbar栏，点击Toolbar中预定义好的设备图标将在该设备大小尺寸屏幕中显示页面。

实际标签内容为以下Javascript代码（默认），官网页面底部可定制Toolbar栏中显示设备，嗯，可以看出，offline不好用，必须有internet链接能加载到`resizer.min.js`文件。
{% highlight JavaScript %}
javascript: void((function(d) {
    if (self != top || d.getElementById('toolbar') && d.getElementById('toolbar').getAttribute('data-resizer')) return false;
    d.write('<!DOCTYPE HTML><html style="opacity:0;"><head><meta charset="utf-8"/></head><body><a data-viewport="320x480" data-icon="mobile">Mobile (e.g. Apple iPhone)</a><a data-viewport="320x568" data-icon="mobile" data-version="5">Apple iPhone 5</a><a data-viewport="600x800" data-icon="small-tablet">Small Tablet</a><a data-viewport="768x1024" data-icon="tablet">Tablet (e.g. Apple iPad 2-3rd, mini)</a><a data-viewport="1280x800" data-icon="notebook">Widescreen</a><a data-viewport="1920×1080" data-icon="tv">HDTV 1080p</a><script src="http://lab.maltewassermann.com/viewport-resizer/resizer.min.js"></script></body></html>')
})(document));
{% endhighlight %}



就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
