---
layout: post
title: "使用Nexus搭建maven私服"
categories: maven
---

#### Windows2008R2环境，使用Nexus搭建Maven私服，经测试实践无错记录。
<br />
其中所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1sjz5aW1)供下载。
>* apache-maven-3.2.3-bin.zip
>* nexus-2.10.0-02-bundle.zip

首先，下载并解压缩maven到C:\apache-maven-3.2.3，配置环境变量MAVEN_HOME=C:\apache-maven-3.2.3并添加到PATH，mvn -v验证maven是否可以正确执行。

下载并解压缩nexus到C:\nexus-2.10.0-02-bundle，配置环境变量NEXUS_HOME=C:\nexus-2.10.0-02-bundle\nexus-2.10.0-02并添加到PATH，NEXUS的目录结构大致如下所示：

	C:\nexus-2.10.0-02-bundle
		|- nexus-2.10.0-02
		|- sonatype-work

接下来配置Nexus：
>* 如果当前没有设置JAVA_HOME环境变量，则需指定java所在目录，还是配个JAVA_HOME吧

	%NEXUS_HOME%\bin\jsw\conf\wrapper.conf: wrapper.java.command=java

>* 端口默认是8081，在这里可以改

	%NEXUS_HOME%\conf\nexus.properties：application-port

>* Nexus默认工作目录是sonatype-work/nexus

	%NEXUS_HOME%\conf\nexus.properties：nexus-work

现在可以启动Nexus了，默认配置下，浏览器http://IP:8081/nexus访问，使用初始账号可以登录。

	# 安装 / 卸载 nexus service
	nexus.bat install / uninstall

	# 启动 / 停止 nexus
	nexus.bat start / stop


Nexus的一些操作：
>* Nexus预置3个用户：admin，默认密码admin123，用来配置nexus服务；deployment，默认密码deployment123，用于上传部署构件但无法配置nexus；anonymous，匿名用户，即使在未登录状态亦可浏览和搜索仓库。

>* 开启Nexus远程索引：download remote index = true

>* 手工加入第三方jar包：View/Repositories->Repositories->3rdParty->AntifactUpload->GAVParameters

接下来，在settings.xml中配置，使得Nexus成为私服：
{% highlight xml %}
<profiles>
	<profile>
    	<id>nexus</id>
		<repositories>
      		<repository>
        		<id>nexus</id>
        		<name>nexus repos</name>
        		<url>http://nexus</url><!-- mirrorOf * url已失去意义 -->
        		<releases><enabled>true</enabled></releases>
        		<snapshots><enabled>true</enabled></snapshots>
			</repository>
		</repositories>
		<pluginRepositories>
        	<pluginRepository>
				<id>nexus</id>
				<name>nexus plugin repos</name>
				<url>http://nexus</url><!-- mirrorOf * url已失去意义 -->
				<releases><enabled>true</enabled></releases>
				<snapshots><enabled>true</enabled></snapshots>
			</pluginRepository>
		</pluginRepositories>
	</profile>
</profiles>
<!-- 使profile:nexus有效  -->
<activeProfiles>
	<activeProfile>nexus</activeProfile>
</activeProfiles>
<!-- 使mirror:nexus镜像所有已生效profile  -->
<mirrors>
	<mirror>
    	<id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <url>http://IP:8081/nexus/content/groups/public/</url>
	</mirror>
</mirrors>
{% endhighlight %}

验证Nexus私服代理是否成功：

	执行以下命令, 查看downloading和downloaded地址是否来自http://IP:8081/nexus/*，如果是，则代理成功。

生成maven java项目：

	mvn archetype:create -DgroupId=com.test -DartifactId=test-app

生成maven web项目：

	mvn archetype:create -DgroupId=com.test -DartifactId=test-webapp -DarchetypeArtifactId=maven-archetype-webapp

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
