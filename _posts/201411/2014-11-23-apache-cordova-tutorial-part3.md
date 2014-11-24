---
layout: post
title: "Apache Cordova tutorial part3"
categories: Cordova
---

前面两篇[apache-cordova-tutorial-part1](/20141116/apache-cordova-tutorial-part1/) 和 [apache-cordova-tutorial-part2](/20141120/apache-cordova-tutorial-part2/)已经将项目初步运行起来了。

接下来，创建几个页面视图使得`Employee Directory`活起来。

创建Home页面视图，用于嵌套显示Employee列表，于是新建文件`js/HomeView.js`，定义传参EmployeeService构造函数，内嵌Employee列表页面视图，如下：
	
	var HomeView = function (service) {

		// nested view to display the list of employees
		var employeeListView;

		this.initialize = function () {
	        // define a div wrapper for the view (used to attach view-related events)
	        this.$el = $('<div/>');
	        this.$el.on('keyup', '.search-key', this.findByName);
	        // instantiate the nested view
	        employeeListView = new EmployeeListView();
	        this.render();
    	};

    	// move the app.js renderHomeView() function here
    	this.render = function() {
	        this.$el.html(this.template());
	        $('.content', this.$el).html(employeeListView.$el);
	        return this;
    	};

    	// move the app.js findByName() function here
    	this.findByName = function() {
	        service.findByName($('.search-key').val()).done(function(employees) {
	            employeeListView.setEmployees(employees);
	        });
    	};

    	this.initialize();
	}
创建EmployeeList页面视图，用于显示Employee列表，于是新建文件`js/EmployeeListView.js`，如下：

	var EmployeeListView = function () {

	    var employees;

	    this.initialize = function() {
	        this.$el = $('<div/>');
	        this.render();
	    };

	    this.setEmployees = function(list) {
	        employees = list;
	        this.render();
	    }

	    this.render = function() {
	        this.$el.html(this.template(employees));
	        return this;
	    };

	    this.initialize();
	}

测试一下上面的HomeView和EmployeeListView两个页面视图：

修改index.html，在引入app.js前边，添加引入：

	<script src="js/EmployeeListView.js"></script>
	<script src="js/HomeView.js"></script>
修改app.js，移除renderHomeView()和findByName()函数，修改原来的模板初始化及service初始化逻辑：

	HomeView.prototype.template = Handlebars.compile($("#home-tpl").html());
	EmployeeListView.prototype.template = Handlebars.compile($("#employee-list-tpl").html());

	service.initialize().done(function () {
    	$('body').html(new HomeView(service).render().$el);
	});

重新编译并运行模拟器，OK，没什么问题。接下来，实现点击某一Employee，路由到该Employee页面视图，查看详细。

修改index.html，添加Employee模板，如下：
	{% raw %}
	<script id="employee-tpl" type="text/template">
	    <header class="bar bar-nav">
	        <a class="btn btn-link btn-nav pull-left" href="#">
	            <span class="icon icon-left-nav"></span>
	        </a>
	        <h1 class="title">Employee</h1>
	    </header>
	    <div class="content">
	        <div class="card">
	            <ul class="table-view">
	                <li class="table-view-cell media">
	                    <img class="media-object pull-left" src="assets/pics/{{pic}}">
	                    <div class="media-body">
	                        {{ firstName }} {{ lastName }}
	                        <p>{{ title }}</p>
	                    </div>
	                </li>
	                <li class="table-view-cell media">
	                    <a href="tel:{{ officePhone }}" class="push-right">
	                        <span class="media-object pull-left icon icon-call"></span>
	                        <div class="media-body">
	                            Call Office
	                            <p>{{ officePhone }}</p>
	                        </div>
	                    </a>
	                </li>
	                <li class="table-view-cell media">
	                    <a href="tel:{{ cellPhone }}" class="push-right">
	                        <span class="media-object pull-left icon icon-call"></span>
	                        <div class="media-body">
	                            Call Cell
	                            <p>{{ cellPhone }}</p>
	                        </div>
	                    </a>
	                </li>
	                <li class="table-view-cell media">
	                    <a href="sms:{{ cellPhone }}" class="push-right">
	                        <span class="media-object pull-left icon icon-sms"></span>
	                        <div class="media-body">
	                            SMS
	                            <p>{{ cellPhone }}</p>
	                        </div>
	                    </a>
	                </li>
	                <li class="table-view-cell media">
	                    <a href="mailto:{{ email }}" class="push-right">
	                        <span class="media-object pull-left icon icon-mail"></span>
	                        <div class="media-body">
	                            Email
	                            <p>{{ email }}</p>
	                        </div>
	                    </a>
	                </li>

	            </ul>
	        </div>
	    </div>
	</script>

创建EmployeeView页面视图，用于显示Employee详细，于是新建`js/EmployeeView.js`文件，如下：

	var EmployeeView = function(employee) {

	  this.initialize = function() {
	      this.$el = $('<div/>');
	  };

	  this.render = function() {
	      this.$el.html(this.template(employee));
	      return this;
	  };

	  this.initialize();

	}
修改index.html，在引入app.js前边，添加引入：

	<script src="js/EmployeeView.js"></script>

实现在EmployeeList页面视图中点击某一Employee，路由到EmployeeView页面视图，查看详细：

修改index.html，在引入jquery.js后边，添加引入：

	<script src="js/EmployeeView.js"></script>
修改app.js，添加Employee模板：
	EmployeeView.prototype.template = Handlebars.compile($("#employee-tpl").html());
定义两个视路由规则，如下：

    service.initialize().done(function () {
        router.addRoute('', function() {
            console.log('empty');
            $('body').html(new HomeView(service).render().$el);
        });

        router.addRoute('employees/:id', function(id) {
            console.log('details');
            service.findById(parseInt(id)).done(function(employee) {
                $('body').html(new EmployeeView(employee).render().$el);
            });
        });

        router.start();
    });

重新编译并运行模拟器，OK，测试，点击某一Employee路由到该Employee页面视图，点击左上角回退图标，回退到EmployeeList页面视图，搞定。

硬件加速优化，实现页面视图之间的快速切换：

修改index.html文件，添加对pageslider.css和pageslider.js(在jquery.js后边)的引入：
	
	<link href="assets/css/pageslider.css" rel="stylesheet">

	<script src="lib/pageslider.js"></script>
修改app.js文件，定义PageSlider对象实例，并在路由中替换`$('body').html()`为`slider.slidePage()`，如下：
	
	var slider = new PageSlider($('body'));
    service.initialize().done(function () {
        router.addRoute('', function() {
            console.log('empty');
            slider.slidePage(new HomeView(service).render().$el);
        });

        router.addRoute('employees/:id', function(id) {
            console.log('details');
            service.findById(parseInt(id)).done(function(employee) {
                slider.slidePage(new EmployeeView(employee).render().$el);
            });
        });

        router.start();
    });

重新编译并运行模拟器，测试，原有功能没有问题，页面视图切换也流畅了很多，还有了些动态效果。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
