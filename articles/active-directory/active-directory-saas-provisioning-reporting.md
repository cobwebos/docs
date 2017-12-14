---
title: "有关在  SaaS 应用程序中自动预配 Azure Active Directory 用户帐户的报告 | Microsoft Docs"
description: "了解如何检查自动用户帐户预配作业的状态，以及如何排查单个用户的预配问题。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: d2ec1b25e39717fb2411d35e3b0a9f5eb3a977be
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>教程：有关自动用户帐户预配的报告


Azure Active Directory 包含一个[用户帐户预配服务](active-directory-saas-app-provisioning.md)，可帮助在 SaaS 应用和其他系统中自动预配/取消预配用户帐户，以进行端到端的标识生命周期管理。 Azure AD 支持对 [Azure AD 应用程序库](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)的“特色”部分中的所有应用程序和系统使用预先集成的用户预配连接器。

本文介绍如何在设置预配作业后检查其状态，以及如何排查单个用户和组的预配问题。

## <a name="overview"></a>概述

预配连接器主要是遵循需要预配用户帐户的应用程序的[随附文档](active-directory-saas-tutorial-list.md)，使用 [Azure 管理门户](https://portal.azure.com)设置和配置的。 为应用程序配置并运行预配作业后，可使用以下两种方法之一报告这些作业的状态：

* **Azure 管理门户** - 本文主要介绍如何从 [Azure 管理门户](https://portal.azure.com)检索报告信息，其中提供了预配摘要报告，以及给定应用程序的详细预配审核日志。

* **审核 API** - Azure Active Directory 还提供审核的 API，用于以编程方式检索详细预配审核日志。 请参阅 [Azure Active Directory 审核 API 参考](active-directory-reporting-api-audit-reference.md)，获取有关使用此 API 的具体文档。 尽管本文未专门介绍如何使用该 API，但详细说明了审核日志中记录的预配事件类型。

### <a name="definitions"></a>定义

本文使用以下术语，其定义如下：

* **源系统** - Azure AD 预配服务从中同步的用户存储库。 Azure Active Directory 是大多数预先集成的预配连接器的源系统，但有一些例外（示例：Workday 入站同步）。

* **目标系统** - Azure AD 预配服务要同步到的用户存储库。 此系统通常是某个 SaaS 应用程序（示例：Salesforce、ServiceNow、Google Apps、Dropbox for Business），但在某些情况下，也可能是 Active Directory 等本地系统（示例：目标为 Active Directory 的 Workday 入站同步）。


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>从 Azure 管理门户获取预配报告

要获取给定应用程序的预配报告信息，首先请启动 [Azure 管理门户](https://portal.azure.com)，然后浏览到为其配置了预配的企业应用程序。 例如，如果要在 LinkedIn Elevate 中预配用户，应用程序详细信息的导航路径为：

“Azure Active Directory”>“企业应用程序”>“所有应用程序”>“LinkedIn Elevate”

在此处可以访问预配摘要报告和预配审核日志，下面对此做了介绍。


### <a name="provisioning-summary-report"></a>预配摘要报告

预配摘要报告显示在给定应用程序的“预配”选项卡中。 它位于“同步详细信息”部分中的“设置”下面，提供以下信息：

* 已同步并且当前位于源系统与目标系统之间的预配范围内的用户和/或组总数。

* 上次运行同步的时间。 完全同步完成后，同步通常每隔 20-40 分钟发生一次。

* 是否已完成初始完全同步。

* 预配过程是否已被隔离，隔离状态的原因是什么（例如，由于管理员凭据无效，与目标系统通信失败）

管理员应该先查看预配摘要报告，确定预配作业的工作运行状况。

 ![摘要报告](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>预配审核日志
预配服务执行的所有活动记录在 Azure AD 审核日志中，可在“审核日志”选项卡中的“帐户预配”类别下面查看这些日志。 记录的活动事件类型包括：

* **导入事件** - 每当 Azure AD 预配服务从源系统或目标系统检索有关单个用户或组的信息时，将记录“导入”事件。 在同步期间，将先从源系统检索用户，其结果记录为“导入”事件。 然后，将会针对目标系统查询已检索的用户的匹配 ID，以检查这些 ID 是否存在，其结果也会记录为“导入”事件。 这些事件记录发生相应事件时，Azure AD 预配服务所看到的所有已映射用户属性及其值。 

* **同步规则事件** - 导入用户数据并在源系统和目标系统中评估这些数据后，这些事件将报告属性映射规则以及配置的任何范围筛选器的结果。 例如，如果源系统中的某个用户被视为在预配范围内，同时被视为不在目标系统内，则该事件将记录不会在目标系统中预配该用户。 

* **导出事件** - 每当 Azure AD 预配服务向目标系统写入用户帐户或组对象时，将记录“导出”事件。 这些事件记录发生相应事件时，Azure AD 预配服务写入的所有用户属性及其值。 如果向目标系统写入用户帐户或组对象时出错，此处会显示该错误。

* **进程托管事件** - 如果预配服务在尝试某个操作时遇到失败，并根据退让时间间隔重试该操作，则会发生进程托管。 每当某个预配操作被停用时，将记录“托管”事件。

查看单个用户的预配事件时，这些事件通常按以下顺序显示：

1. 导入事件：已从源系统检索用户。

2. 导入事件：已查询目标系统以检查检索的用户是否存在。

3. 同步规则事件：已根据配置的属性映射规则和范围筛选器评估源和目标系统中的用户数据，以确定应执行哪个操作（如果有）。

4. 导出事件：如果同步规则事件指明应执行某个操作（例如添加、更新、删除），则会在导出事件中记录该操作的结果。

![创建 Azure AD 测试用户](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>查找特定用户的预配事件

预配审核日志的最常见用例是检查单个用户帐户的预配状态。 若要查找特定用户的最近预配事件，请执行以下操作：

1. 转到“审核日志”部分。

2. 在“类别”菜单中，选择“帐户预配”。

3. 在“日期范围”菜单中，选择要搜索的日期范围

4. 在“搜索”栏中，输入要搜索的用户的用户 ID。 ID 值的格式应该与在属性映射配置中选作主要匹配 ID（例如 userPrincipalName 或员工 ID 编号）的任何值相匹配。 所需 ID 值会显示在“目标”列中。

5. 按 Enter 开始搜索。 首先会返回最近的预配事件。

6. 如果返回了事件，请记下活动的类型及其成功或失败状态。 如果未返回任何结果，则表示该用户不存在，或者尚未由预配过程检测到（在尚未完成完全同步的情况下）。

7. 单击单个事件可查看更多详细信息，包括发生事件期间检索、评估或写入的所有用户属性。


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>有关查看预配审核日志的提示

为了尽可能方便地在 Azure 门户查看信息，请选择“列”按钮，并选择以下各列：

* **日期** - 显示事件发生的日期。
* **目标** - 显示作为事件主题的应用名称和用户 ID。
* **活动** - 如前所述的活动类型。
* **状态** - 事件的成功或失败状态。
* **状态原因** - 解释预配事件中发生的情况的摘要。


## <a name="troubleshooting"></a>故障排除

在排查各种用户帐户预配问题时，预配摘要报告和审核日志能够为管理员带来很大的帮助。

有关如何排查自动用户预配问题的基于方案的指导，请参阅[在应用程序中配置和预配用户时出现问题](active-directory-application-provisioning-content-map.md)。


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)
