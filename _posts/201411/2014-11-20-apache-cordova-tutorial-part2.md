---
layout: post
title: "Apache Cordova tutorial part2"
categories: Cordova
---

前面已经在Windows7 64bits系统中将Android环境搭建好，并把HelloCordova项目运行起来了，在接下来的几篇中，创建并丰富`Employee Directory`项目。

创建`XXX Employee Directory`项目可参考前篇[apache cordova tutorial part1](/20141116/apache-cordova-tutorial-part1/)，略去不表。

在项目代码中，使用Json, LocalStorage, Memory和Websql共4中方式进行数据存取，修改`index.html`文件，引入下面不同的JavaScript文件，分别进行测试。
	
	Json方式：`js/services/json/EmployeeService.js`
	LocalStorage方式：`js/services/localstorage/EmployeeService.js`
	Memory方式： `js/services/memory/EmployeeService.js`
	Websql方式：`js/services/websql/EmployeeService.js`

测试Json方式时，切换到server目录，执行以下命令启动Server，同时需要注意输出Json格式数据的serviceURL。

	# install the server dependencies
	npm install

	# start the server
	node server

点击Help按钮，默认的Alert框如左下图所示，接下来，将其修改成右下图示的Alert框：

![示例](/images/alertOrigin.png)   ![示例](/images/alertOverrided.png)	
	
安装dialogs插件
	
	cordova plugin add org.apache.cordova.dialogs

修改index.html，引入cordova.js文件

	<script src="cordova.js"></script>

修改js/app.js文件，将下面代码添加到"Event Registration"块，Override重写window.alert方法

{% highlight javascript %}
document.addEventListener('deviceready', function () {
  	if (navigator.notification) { // Override default HTML alert with native dialog
      	window.alert = function (message) {
          	navigator.notification.alert(
              	message,    // message
              	null,       // callback
              	"Employee Directory", // title
              	'OK'        // buttonName
          	);
      	};
  	}
}, false);
{% endhighlight %}

重新编译`Employee Directory`项目，启动Android模拟器，再次点击Help按钮，Alert变漂亮了。

在Android环境，尤其是iOS环境中，点击Help按钮明显有300ms的延迟，引入fastclick.js以避免。

index.html中引入fastclick.js
	
	<script src="lib/fastclick.js"></script>

将FastClick注册到deviceready事件
{% highlight javascript %}
document.addEventListener('deviceready', function () {
	FastClick.attach(document.body);
  	if (navigator.notification) { // Override default HTML alert with native dialog
      	window.alert = function (message) {
          	navigator.notification.alert(
              	message,    // message
              	null,       // callback
              	"Employee Directory", // title
              	'OK'        // buttonName
          	);
      	};
  	}
}, false);
{% endhighlight %}
编译测试，点击Help按钮没有300ms的延迟了。

接下来，通过建立`Single-Page Application`（如左下图）并使用Handlebars模板（如右下图）将页面丰富起来。

![示例](/images/singlepageapp.png)   ![示例](/images/singlepageapphb.png)	

建立Single-Page Application

修改index.html，删除body中script标签以外的其它p和div等标签内容
	
	<script src="cordova.js"></script>

修改app.js，将下面renderHomeView()添加到findByName()后面
{% highlight javascript %}
function renderHomeView() {
    var html =
      	"<h1>Directory</h1>" +
      	"<input class='search-key' type='search' placeholder='Enter name'/>" +
      	"<ul class='employee-list'></ul>";
    $('body').html(html);
    $('.search-key').on('keyup', findByName);
}

// 修改EmployeeService初始化逻辑
var service = new EmployeeService();
service.initialize().done(function () {
	// console.log("Service initialized");
    renderHomeView();
});

/* 注释或删除多余的事件注册
$('.search-key').on('keyup', findByName);
$('.help-btn').on('click', function() {
	alert("Employee Directory v3.4");
});
*/
{% endhighlight %}
重新编译并测试，界面如上图左所示，挺丑的，下面使用Handlebars模板将页面变漂亮些。

修改index.html，引入和定义handlebars.js模板

在fastclick.js引入下面添加引入handlebars.js，如下所示

	<script src="cordova.js"></script>
	<script src="lib/fastclick.js"></script>
	<script src="lib/handlebars.js"></script>
	<script src="lib/jquery.js"></script>
	<script src="js/services/memory/EmployeeService.js"></script>
	<script src="js/app.js"></script>

在head部分添加引入ratchet.css和styles.css，如下所示
{% highlight html %}
<head>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">
	<link href="assets/ratchet/css/ratchet.css" rel="stylesheet">
	<link href="assets/css/styles.css" rel="stylesheet">
</head>
{% endhighlight %}

在body中，script等标签上面位置，添加home-tpl和employee-list-tpl的界面显示layout模板，如下所示
	
	{% raw %}
	<script id="home-tpl" type="text/template">
	    <header class="bar bar-nav">
	        <h1 class="title">Directory</h1>
	    </header>
	    <div class="bar bar-standard bar-header-secondary">
	        <input class='search-key' type="search"/>
	    </div>
	    <div class="content"></div>
	</script>

	<script id="employee-list-tpl" type="text/template">
	    <ul class="table-view">
	        {{#each this}}
	        <li class="table-view-cell media">
	          <a href="#employees/{{ id }}">
	              <img class="media-object pull-left" src="assets/pics/{{pic}}">
	              <div class="media-body">
	                  {{firstName}} {{lastName}}
	                  <p>{{title}}</p>
	              </div>
	          </a>
	        </li>
	        {{/each}}
	    </ul>
	</script>
修改app.js，使用handlebars.js模板

在EmployeeService初始化前，定义并赋值模板变量homeTpl和employeeListTpl

	var homeTpl = Handlebars.compile($("#home-tpl").html());
	var employeeListTpl = Handlebars.compile($("#employee-list-tpl").html());

修改findByName()和renderHomeView()函数为下面内容

	function findByName() {
	    service.findByName($('.search-key').val()).done(function (employees) {
	        $('.content').html(employeeListTpl(employees));
	    });
	};

	function renderHomeView() {
	    $('body').html(homeTpl());
	    $('.search-key').on('keyup', findByName);
	}

重新编译并测试，界面如右图左所示，看起来还不错，挺漂亮的。

在iOS7中，如左下图示，StatusBar和应用会有重叠导致界面显示瑕疵，安装使用statusbar插件来解决。

![示例](/images/statusbarOverlapped.png)   ![示例](/images/statusbarOverlapFixed.png)	

	# 安装statusbar插件
	cordova plugins add org.apache.cordova.statusbar

	# 修改app.js，将下面代码加入deviceready事件
	StatusBar.overlaysWebView( false );
	StatusBar.backgroundColorByHexString('#ffffff');
	StatusBar.styleDefault();
重新编译并在iOS模拟器上测试，嗯，没问题了，如右上图示。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
