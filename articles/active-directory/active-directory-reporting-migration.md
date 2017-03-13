---
title: "如何在 Azure 门户中查找活动报表 | Microsoft 文档"
description: "了解如何在 Azure 门户中查找活动报表"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: afd48a77faeeab6d5a8730934345cb7000a59831
ms.openlocfilehash: e72e49140aaad55a6e66a9f331ce7fde20b8577c
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>如何在 Azure 门户中查找活动报表

通过从 Azure 经典门户迁移到 Azure 门户，我们为 Azure Active Directory 活动日志提供了新的外观。 我们在几个月前已经发布了一篇[博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)来说明我们如何在你正在处理的资源的上下文中提供活动日志。 本文介绍了我们如何将 Azure 经典门户中的现有报表转换成了 Azure 门户中的新报表。

## <a name="what-is-new"></a>有哪些新内容？

Azure 经典门户中的报表划分为各种类别：

1.    安全报表
2.    活动报表
3.    集成的应用报表

### <a name="activity-and-integrated-app-reports"></a>活动和集成的应用报表

发展到 Azure 门户中基于上下文的报告，我们已将现有报表合并到单个视图中，并使用单个基础 API 向此视图提供数据。 

可以在 **Azure Active Directory** 边栏选项卡的“活动”部分中的“审核日志”下找到此视图。


![审核日志](./media/active-directory-reporting-migration/482.png "审核日志")








下面是已合并到此视图中的报表。

-    审核报表

-     密码重置活动

-     密码重置注册活动

-     自助服务组活动

-     Office365 组名称更改

-     帐户设置活动

-     密码滚动更新状态
-     帐户设置错误


应用程序使用情况报表已增强并包含在名为“登录”的视图中。 可以在 **Azure Active Directory** 边栏选项卡的“活动”部分中找到此视图。


![审核日志](./media/active-directory-reporting-migration/483.png "审核日志")

此视图包含所有用户的登录名，从而可以派生此视图以获取应用程序使用情况信息。 也可以通过“企业应用程序”概述查找应用程序使用情况信息。

![审核日志](./media/active-directory-reporting-migration/484.png "审核日志")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>如何访问此单一视图中的特定报表？

### <a name="audit-logs"></a>审核日志

许多客户的关键诉求之一是要求能够使用多个筛选器选项来访问 Azure AD 中的活动日志。 我们已取代了此功能，并为你提供了高级筛选机制来筛选你需要的数据。 我们提供的其中一个筛选器名为“活动类别”，其中列出了 Azure AD 提供的各种类型的活动日志。 通过选择所需的类别，可以将结果的范围缩小到要查找的内容。 

例如，如果仅希望获取与**自助服务密码重置**相关的活动，则可以选择“自助服务密码管理”类别。 你可以看到的类别取决于你正在处理的资源。  


![审核日志](./media/active-directory-reporting-migration/06.png "审核日志")

 
目前提供的各种类别包括：

- 核心目录

- 自助服务密码管理

- 自助服务组管理

- 帐户预配

### <a name="application-usage"></a>应用程序使用情况

可以通过“活动”->“登录”视图查看所有应用或单个应用的应用程序使用情况。 如下所示，可以针对所有应用程序或单个应用程序呈现此视图。 如果希望缩小结果范围，可以按“用户名”或“应用程序名称”进行筛选。
 

![审核日志](./media/active-directory-reporting-migration/07.png "审核日志")


### <a name="security-reports"></a>安全报表

安全报表已合并，以提供 Azure Active Directory 可以检测和报告的所有安全相关风险事件视图的完整概述。 有关完整概述，请参阅 [Azure Active Directory 风险事件](active-directory-identity-protection-risk-events.md)。  
在本主题中，可以找到 Azure Active Directry 异常活动报告如何映射到 [Azure AD 异常活动报告](active-directory-identity-protection-risk-events.md#azure-ad-anomalous-activity-reports)部分中 Azure AD 中的风险事件的概述。

在 Azure 门户中，可以访问有关 **Azure Active Directory** 边栏选项卡的“安全性”部分中检测到的风险事件的报告。 跟踪以下报告中检测到的风险事件：   

- 有风险的用户
- 有风险的登录 


![审核日志](./media/active-directory-reporting-migration/04.png "审核日志")

有关这些报告的详细信息，请参阅：

- [Azure Active Directory 门户中的“有风险的用户”安全报表 - 预览版](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 门户中的“有风险的登录”报表 - 预览版](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure 经典门户中的与 Azure 门户中的活动报表之对比

本部分列出了 Azure 经典门户中的现有报表并说明了在 Azure 门户中如何获取该信息。


所有审核数据的入口点为 **Azure Active Directory** 边栏选项卡的“活动”部分中的“审核日志”。

![审核日志](./media/active-directory-reporting-migration/61.png "审核日志")


| Azure 经典门户                 | Azure 门户步骤                                                         |
| ---                                  | ---                                                                        |
| 审核日志                           | 对于“活动类别”，选择“核心目录”。                       |
| 密码重置活动              | 对于“活动类别”，选择“自助服务密码管理”。 | 
| 密码重置注册活动 | 对于“活动类别”，选择“自助服务密码管理”。     |
| 自助服务组活动         | 对于“活动类别”，选择“自助服务组管理”。        |
| 帐户设置活动        | 对于“活动类别”，选择“帐户用户预配”。         |
| 密码滚动更新状态             | 对于“活动类别”，选择“自动化应用密码滚动更新”。      |
| 帐户设置错误          | 对于“活动类别”，选择“帐户用户预配”。        |
| Office365 组名称更改         | 对于“活动类别”，选择“自助服务密码管理”；对于“活动资源类型”，选择“组”；对于“活动源”，选择“O365 组”。|

 

“应用程序使用情况”报表的入口点是“Azure Active Directory”>“企业应用程序”>“登录”。 


![审核日志](./media/active-directory-reporting-migration/199.png "审核日志")


