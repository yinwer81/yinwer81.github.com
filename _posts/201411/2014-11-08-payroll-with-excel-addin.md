---
layout: post
title: "EXCEL加载项 payroll"
categories: programming
---

公司还没有上专业的工资管理系统，我们提供了一个简单的EXCEL Payroll程序以简化其工作：
>* 专人手工在EXCEL模板中维护员工每月工资和奖金数据
>* 每月放粮日通过VBA Addin发送Payroll邮件，一封为加密过的Payslip附件，另一封为附件密码



**邮件发送程序依赖于CDO，故不支持EXCEL2007以上版本。**


待加强的地方：
1. 以HTML格式发送邮件
2. 检查邮箱有效性
3. 标记发送失败邮件

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
