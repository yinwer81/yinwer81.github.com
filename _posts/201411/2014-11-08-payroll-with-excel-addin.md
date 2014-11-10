---
layout: post
title: "EXCEL加载项Payroll"
categories: programming
---

公司还没有上专业的工资管理系统，我们提供了一个简单实用的Payroll程序以支持其工作：
>* 专人手工在基于EXCEL的Payroll模板(Payroll.xlsx)中维护员工工资和奖金数据
>* 每月放粮日通过Addin程序发送邮件，每人2封，一封为加密过的Payslip(EXCEL附件)，另一封为附件密码

Payroll程序非常简单，一个EXCEL的Payroll模板(Payroll.xlsx)和一个EXCEL Addin程序(Payroll.xla)，见下图：
![示例](/images/payrollList.png)

**邮件发送程序依赖于CDO，故不支持EXCEL2007以上版本。**我这里是EXCEl2013版本，仅作演示用。
![示例](/images/payrollSheet.png)





Features to be enhanced：
1. 以HTML格式发送邮件
2. 检查邮箱有效性
3. 标记发送失败邮件

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
