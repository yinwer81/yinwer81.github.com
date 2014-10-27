---
layout: post
title: "分分钟走你Sonar"
---

Sonar是一个源代码质量管理平台, 且其本身open source。<br>
Sonar通过插件机制(如: Java Ecosystem插件集合), 集成代码测试检查分析/CI等工具, 对代码测试检查分析的结果进行再加工处理, 以量化的方式度量代码质量的变化, 以方便地对不同规模种类的项目进行代码质量管理。<br>

Sonar官方页面: [戳这里](http://www.sonarqube.org/), 最新版本: 3.7.2, 发布日期: 2013/10/02<br>
解压缩到c:/sonar-3.7.2, mysql创建数据库sonar, 编辑/conf/sonar.properties进行以下配置:<br>
	#数据库
	sonar.jdbc.username:  root
	sonar.jdbc.password:  passw0rd
	sonar.jdbc.url:       jdbc:mysql://localhost:3306/sonar

	#服务器
	sonar.web.host: 127.0.0.1
	sonar.web.port: 9000
	sonar.web.context: /sonar
双击bin/windows-x86-64/StartSonar.bat会自动创建数据库表结构等。<br>
浏览器访问: http://127.0.0.1:9000/sonar<br>

Sonar默认管理员帐号密码: admin/admin, 成功登录后, 在菜单: 配置->系统(更新中心)->Installed Plugins可看到当前已安装的所有插件。<br>

中文语言包:<br>
下载[这里](http://docs.codehaus.org/display/SONAR/Chinese+Pack), 扔到/extensions/plugins/, 重启Sonar,将根据浏览器语言设置显示中文/英文。

Java Ecosystem插件集合:<br>
下载[这里](http://docs.codehaus.org/display/SONAR/Java+Ecosystem), 扔到/extensions/plugins/, 重启Sonar<br>

OK, 接下来, 参考[这里](http://docs.codehaus.org/display/SONAR/Analyzing+Source+Code#AnalyzingSourceCode-RunningAnalysis), 可以看到Sonar提供多种类型的客户端以根据项目构建类型进行代码检查和分析。<br>

Non-maven项目(Java示例项目: [示例地址](/downloads/testSonar.zip))<br>
下载[这里](http://www.sonarqube.org/downloads/)sonar-runner, 最新版本: 2.3, 发布日期: 2013/07/23<br>
解压缩到${SONAR_HOME}\sonar-runner-2.3, 配置环境变量SONAR_RUNNER_HOME(bin目录), 修改conf\sonar-runner.properties<br>
	sonar.host.url = http://localhost:9000/sonar
	sonar.jdbc.url = jdbc:mysql://localhost:3306/sonar
	sonar.jdbc.username = root
	sonar.jdbc.password = passw0rd
命令行下执行sonar-runner -h, 正确输出sonar相关信息即配置成功。

在示例项目根目录下, 配置sonar-runner.properties, 命令行执行sonar-runner, 详细请参考示例项目。

Maven项目(Github示例项目: [clone地址](http://github.com/dunkcoder/insertThenFetch.git))<br>
编辑${MAVEN_HOME}\conf\settings.xml(版本: 3.1.0), 增加以下Sonar Profile配置信息:
{% highlight xml %}
<profile>
	<id>sonar</id>
    <activation>
    	<activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <sonar.jdbc.url>jdbc:mysql://localhost:3306/sonar</sonar.jdbc.url>
        <sonar.jdbc.driver>com.mysql.jdbc.Driver</sonar.jdbc.driver>
        <sonar.jdbc.username>root</sonar.jdbc.username>
        <sonar.jdbc.password>passw0rd</sonar.jdbc.password>
        <sonar.host.url>http://localhost:9000/sonar</sonar.host.url>
    </properties>
</profile>
{% endhighlight %}
在MAVEN工程下面执行

	mvn clean install -Dmaven.test.skip=true sonar:sonar

以上, 后面将分享更多sonar使用实践。