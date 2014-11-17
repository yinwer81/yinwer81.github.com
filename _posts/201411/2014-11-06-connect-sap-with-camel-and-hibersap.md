---
layout: post
title: "Connect sap with camel and hibersap"
categories: programming
---

随着公司业务快速发展，应用企业应用集成(EAI, Enterprise Application Integration)提高IT业务应用系统的可扩展性和可维护性逐显重要。

前篇[使用hibersap从SAP中读数据](/20141021/connect-sap-with-hibersap/)中讲过怎样使用hibersap及其插件(plugin-hibersap)快速生成Java代码后，导入项目中测试。本文在`Apache Camel`中通过Hibersap自定义组件，连接SAP调用RFC和BAPI数据交换进行POC(Proof of Concept)测试，项目代码在[这里](https://github.com/yinwer81/camelsapexample)。

例子程序环境如下（参考pom.xml）：
>* jdk.version 1.7
>* camel.version 2.9.8
>* spring.version 3.0.6.RELEASE
>* sapjco.version 3.0.10
>* hibersap.version 1.2.0-Beta03
>* maven-bundle-plugin.version 2.5.3

#### 自定义Camelsap组件3步：
<br/>
从DefaultComponent继承，定义CamelSapComponent类，覆盖createEndpoint方法。<br/>
从DefaultEndpoint继承，定义CamelSapEndpoint类，覆盖构造方法和createProducer提供Producer对象。<br/>
从DefaultProducer继承，定义CamelSapProducer类，实现AsyncProcessor类，提供异步调用。

接下来，配置camelsap组件：

	在`META-INF\services\org\apache\camel\component\`下创建camelsap文件
	camelsap文件内容为`class=com.dunkcoder.CamelSapComponent`
	在`native\x86_64\`下放置libsapjco3.so(Linux平台)和sapjco3.dll(Windows平台)
	以上文件夹路径都定义在pom.xml文件中

#### 运行例子程序和单元测试
<br/>
运行单元测试之前，请修改hibersap.xml中A12和A13，test-context.xml中A14连接SAP的参数配置。

和前篇[使用hibersap从SAP中读数据](/20141021/connect-sap-with-hibersap/)中一样，还是测试RFC函数`RFC_SYSTEM_INFO`和`STFC_CONNECTION`，以及BAPI函数`BAPI_COMPANYCODE_GETLIST`的调用，具体代码请参考：`CamelSapTest`，这里就不贴了。

本人对Camel也不甚熟悉，对Component,Endpoint,Producer,Consumer及Route等概念的理解是关键，可以参考下面博客/文章：<br/>
[Apache Camel框架入门示例](http://blog.csdn.net/kkdelta/article/details/7231640)<br/>
[Camel自定义组件示例](http://blog.csdn.net/xtayfjpk/article/details/39122349)

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
