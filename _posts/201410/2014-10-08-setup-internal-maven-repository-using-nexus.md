---
layout: post
title: "Setup internal maven repository using nexus"
categories: maven
---

#### 本篇记录在Windows2008R2 64bits环境下，使用Nexus搭建和配置Maven私服。
<br />
所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1sjz5aW1)：
>* apache-maven-3.2.3-bin.zip
>* nexus-2.10.0-02-bundle.zip

首先，下载并解压缩maven到C:\apache-maven-3.2.3，配置环境变量MAVEN_HOME=C:\apache-maven-3.2.3并添加到PATH，mvn -v验证maven是否可以正确执行。

下载并解压缩nexus到C:\nexus-2.10.0-02-bundle，配置环境变量NEXUS_HOME=C:\nexus-2.10.0-02-bundle\nexus-2.10.0-02并添加到PATH，NEXUS的目录结构如下所示：

	C:\nexus-2.10.0-02-bundle
		|- nexus-2.10.0-02
		|- sonatype-work

接下来，配置和启动Nexus：
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

接下来，在settings.xml中配置，使得Nexus成为私服(拷贝后仅修改Nexus指向url就行)：
{% highlight xml %}
<mirrors>
	<mirror>
    	<id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <url>http://IP:8081/nexus/content/groups/public/</url>
	</mirror>
</mirrors>
{% endhighlight %}

验证Nexus私服代理是否成功：查看下载artifacts的地址是否来自镜像URL地址，如果是，则代理成功。

生成maven java项目：

	mvn archetype:create -DgroupId=com.test -DartifactId=test-app

生成maven web项目：

	mvn archetype:create -DgroupId=com.test -DartifactId=test-webapp -DarchetypeArtifactId=maven-archetype-webapp

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
