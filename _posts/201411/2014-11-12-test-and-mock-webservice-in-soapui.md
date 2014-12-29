---
layout: post
title: "Test and mock webservices in soapUI"
categories: WebService
---

本文记录怎样在soapUI中测试SOAP和REST两种方式的WebService，soapUI使用5.0.0版本。

#### 测试SOAP WebService步骤：
<br />
ServiceProvider提供SOAP方式的WSDL Url后，右键点Projects -> New SOAP Project，Initial WSDL中填入WSDL Url，创建测试项目。

我这里以SAP Client=120的[这个WebService](http://escnjysd01.envisioncn.com:8000/sap/bc/srt/wsdl/bndg_E4422CDBB7045DF18753D48564570FA4/wsdl11/allinone/ws_policy/document?sap-client=120)为例说明，右键复制URL，因为附有简单加密，soapUI读取WSDL的时候也需要输入账号和密码，soapUI导航面板中如图所示：

![示例](/images/soapProject.png)
	
逐层打开并双击Request 1进行测试(左侧输入IvMatnr参数后Submit)，参考下图，注意：需要添加Basic Authorization的配置。
![示例](/images/soapProjectTest.png)

#### 在soapUI中SOAP MockService：
<br />
右键点击`ZIF_SOP_MAT_MULTIPLE_READ`，选择`Generate SOAP Mock Service`，创建后配置Host(localhost)和Port(8088)，Mock一个静态的Response，启动MockService，在浏览器中访问`http://localhost:8088`可看到正在运行的soapUI MockServices：mockZIF_SOP_MAT_MULTIPLE_READ。

创建MockRequest，将Url指向`http://localhost:8088/mockZIF_SOP_MAT_MULTIPLE_READ`，修改Request中IvMatnr的值后提交，可返回刚才Mock出来的静态Response。REST的Webservice的Mock过程类似不表。

#### 测试REST WebService步骤：
<br />
ServiceProvider提供REST方式的Url后，右键点Projects -> New REST Project，URI中填入Url，创建测试项目。

我这里将SAP Client=120的[这个WebService](http://escnjysd01.envisioncn.com:8000/sap/bc/srt/wsdl/bndg_E4422CDBB7045DF18753D48564570FA4/wsdl11/allinone/ws_policy/document?sap-client=120)注册配置到Mule Server后，以REST的方式Expose出来，地址在[这里](http://172.16.32.55:8081/fromsap/zifSopMatMultipleRead/v1/ws_rest)，右键复制URL。soapUI导航面板中如图所示：

![示例](/images/restProject.png)

逐层打开并双击Request 1进行测试，使用PostMethod，MediaType选择application/json并输入`{"IvMatnr":"P-XXXXXX"}`后Submit，参考下图：
![示例](/images/restProjectTest.png)

写到这里时发现一个很好的资源pdf文档[接口测试工具SoapUI](/downloads/接口测试工具SoapUI.pdf)，转在这里供各位下载，版权归原作者所有。

就酱，您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
