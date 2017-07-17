---
title: "Azure Active Directory Reporting：入门 | Microsoft Docs"
description: "在 Azure Active Directory Reporting 中列出各种可用报告"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: zh-cn
ms.lasthandoff: 02/24/2017

---
# Azure Active Directory Reporting 入门
<a id="getting-started-with-azure-active-directory-reporting" class="xliff"></a>
## 作用
<a id="what-it-is" class="xliff"></a>
Azure Active Directory (Azure AD) 包括针对目录的安全报表、活动报表和审核报表。 下面是包含的报表列表：

### 安全报表
<a id="security-reports" class="xliff"></a>
* 从未知源登录
* 多次失败后登录
* 从多个地理区域登录
* 从具有可疑活动的 IP 地址登录
* 异常登录活动
* 从可能受感染的设备登录
* 具有异常登录活动的用户

### 活动报表
<a id="activity-reports" class="xliff"></a>
* 应用程序使用情况：摘要
* 应用程序使用情况：详细信息
* 应用程序仪表板
* 帐户设置错误
* 单个用户设备
* 单个用户活动
* 组活动报表
* 密码重置注册活动报表
* 密码重置活动

### 审核报表
<a id="audit-reports" class="xliff"></a>
* 目录审核报表

> [!TIP]
> 有关 Azure AD Reporting 的详细文档，请参阅 [查看访问和使用情况报表](active-directory-view-access-usage-reports.md)。
> 
> 

## 工作原理
<a id="how-it-works" class="xliff"></a>
### 报告管道
<a id="reporting-pipeline" class="xliff"></a>
报告管道包含三个主要步骤。 每当用户登录或进行身份验证时，都将发生下列情况：

* 首先，对用户进行身份验证（成功或失败），相关结果会存储在 Azure Active Directory 服务数据库中。
* 定期处理所有最近的登录。 此时，安全和异常活动算法将搜索所有最近登录中的可疑活动。
* 处理后，通过 Azure 经典门户编写和缓存报表，并提供相关服务。

### 报表生成时间
<a id="report-generation-times" class="xliff"></a>
由于大量身份验证和登录都通过 Azure AD 平台进行处理，因此，处理的最近登录为平均 1 小时内的登录。 在极少数情况下，处理最近的登录可能最多需要 8 小时。

你可以通过检查每个报表顶部的帮助文本查找最近处理的登录。

![每个报表顶部的帮助文本](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> 有关 Azure AD Reporting 的详细文档，请参阅 [查看访问和使用情况报表](active-directory-view-access-usage-reports.md)。
> 
> 

## 入门
<a id="getting-started" class="xliff"></a>
### 登录 Azure 经典门户
<a id="sign-into-the-azure-classic-portal" class="xliff"></a>
首先，需要以全局管理员或合规性管理员身份登录 [Azure 经典门户](https://manage.windowsazure.com)。 此外，你还必须是 Azure 订阅服务管理员或共同管理员，或正在使用“访问 Azure AD”的 Azure 订阅。

### 导航到报表
<a id="navigate-to-reports" class="xliff"></a>
要查看报表，请导航到目录顶部的“报表”选项卡。

如果你是首次查看报表，则将需要先同意对话框中的内容才可查看报表。 这是为了确保你组织内的管理员可以查看此数据，某些国家/地区可能将此数据视为私人信息。

![对话框](./media/active-directory-reporting-getting-started/dialogBox.png)

### 浏览每个报表
<a id="explore-each-report" class="xliff"></a>
导航到每个报表，查看所收集的数据和处理的登录。 可以在此处找到 [所有报表列表](active-directory-reporting-guide.md)。

![所有报表](./media/active-directory-reporting-getting-started/reportsMain.png)

### 将报表下载为 CSV 文件
<a id="download-the-reports-as-csv" class="xliff"></a>
每个报表都可以下载为 CSV（逗号分隔值）文件。 你可以在 Excel、PowerBI 或第三方分析程序中使用这些文件进一步分析你的数据。

若要将任何报表下载为 CSV 文件，请导航到报表并单击底部的“下载”。

![“下载”按钮](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> 有关 Azure AD Reporting 的详细文档，请参阅 [查看访问和使用情况报表](active-directory-view-access-usage-reports.md)。
> 
> 

## 后续步骤
<a id="next-steps" class="xliff"></a>
### 针对异常登录活动自定义警报
<a id="customize-alerts-for-anomalous-sign-in-activity" class="xliff"></a>
导航到目录的“配置”选项卡。

滚动到“通知”部分。

启用或禁用“异常登录电子邮件通知"部分。

![“通知”部分](./media/active-directory-reporting-getting-started/notificationsSection.png)

### 与 Azure AD Reporting API 集成
<a id="integrate-with-the-azure-ad-reporting-api" class="xliff"></a>
请参阅 [Reporting API 入门](active-directory-reporting-api-getting-started.md)。

### 对用户使用 Multi-Factor Authentication
<a id="engage-multi-factor-authentication-on-users" class="xliff"></a>
在报表中选择一个用户。

单击屏幕底部的“启用 MFA”按钮。

![位于屏幕底部的“Multi-Factor Authentication”按钮](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> 有关 Azure AD Reporting 的详细文档，请参阅 [查看访问和使用情况报表](active-directory-view-access-usage-reports.md)。
> 
> 

## 了解详细信息
<a id="learn-more" class="xliff"></a>
### 审核事件
<a id="audit-events" class="xliff"></a>
了解对 [Azure Active Directory Reporting 审核事件](active-directory-reporting-audit-events.md)的目录中的哪些事件进行审核。

### API 集成
<a id="api-integration" class="xliff"></a>
请参阅 [Reporting API 入门](active-directory-reporting-api-getting-started.md)和 [API 参考文档](https://msdn.microsoft.com/library/azure/mt126081.aspx)。

### 联系方式
<a id="get-in-touch" class="xliff"></a>
向 [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) 发送电子邮件，提供反馈、获取帮助或提交你可能遇到的任何问题。

> [!TIP]
> 有关 Azure AD Reporting 的详细文档，请参阅 [查看访问和使用情况报表](active-directory-view-access-usage-reports.md)。
> 
> 


