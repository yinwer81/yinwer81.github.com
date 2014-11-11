---
layout: post
title: "EXCEL加载项Payroll"
categories: programming
---

公司还没有上专业的工资管理系统，我们提供了一个简单实用的Payroll程序以简化和支持其工作。

Payroll程序非常简单，一个EXCEL的Payroll模板(Payroll.xlsx)和一个EXCEL Addin程序(Payroll.xla)，见下图：
![示例](/images/PayrollList.png)

**邮件发送程序依赖于CDO，故不支持EXCEL2007以上版本。**我这里是EXCEl2013版本，仅作演示用。

首先，双击Payroll.xla打开并启用宏(放心没毒)，然后再双击打开Payroll.xlsx模板文件，如图所示，Payroll.xlsx模板中，第1,2行为共用标题栏，从第3行起为工资数据行，另外往右还有不少数据列，分辨率原因没有截图。

由专人手工在基于EXCEL的Payroll模板(Payroll.xlsx)中维护员工工资和奖金数据，每月放粮日，点击加载项中的`发送工资单`按钮，将启动Payroll.xla中的宏程序发送邮件，每人两封，一封为加密过的Payslip(EXCEL附件)，另一封为附件密码。
![示例](/images/PayrollSheet.png)

员工仅以加密过的Payslip(EXCEL附件)形式得到Payroll模板共用标题栏(第1,2行)和**只有自己的工资数据行**，互不影响达到保密要求。

如图所示，两封邮件，HR Payroll密码邮件目前仅支持Text文本格式。
![示例](/images/PayrollMail.png)

如图所示，Payslip(EXCEL附件)命名采取`yyyy-m-d - xxx.xlsx`格式，xxx为随机数字，使用EXCEL加密。
![示例](/images/PayrollAttachment.png)

Features to be enhanced：<br/>
1. 以HTML格式发送邮件<br/>
2. 检查邮箱有效性<br/>
3. 标记发送失败邮件

以上，公司内部正式运营6个月没有问题。代码比较乱，就不公开了，感兴趣的可以与我联系索取。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
