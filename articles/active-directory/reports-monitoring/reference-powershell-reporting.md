---
title: 用于报表的 Azure AD PowerShell cmdlet |Microsoft Docs
description: 用于报告的 Azure AD PowerShell cmdlet 参考。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 372041dc24db4ed66977364e2a2997c7195cca9c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988034"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>用于报告的 Azure AD PowerShell cmdlet

通过 Azure Active Directory (Azure AD) 报表, 你可以在你的方向 (审核日志) 和身份验证数据 (登录日志) 中获取有关所有写入操作的活动的详细信息。 尽管该信息可通过使用 MS 图形 API 获取, 但现在可以使用用于报告的 Azure AD PowerShell cmdlet 来检索相同的数据。

本文概述了用于审核日志和登录日志的 PowerShell cmdlet。

## <a name="audit-logs"></a>审核日志

[审核日志](concept-audit-logs.md)通过日志提供 Azure AD 中各种功能所做的所有更改的可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

你可以使用 "AzureADAuditDirectoryLogs cmdlet" 访问审核日志。


| 应用场景                      | PowerShell 命令 |
| :--                           | :--                |
| 应用程序显示名称      | AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq" Azure AD 云同步 "" |
| 类别                      | AzureADAuditDirectoryLogs-Filter "category eq" 应用程序管理 "" |
| 活动日期时间            | AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| 以上都是              | AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq" Azure AD 云同步 "和类别 eq" 应用程序管理 "和 activityDateTime gt 2019-04-18"|


下图显示了此命令的示例。 

![“数据摘要”按钮](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>登录日志

[登录](concept-sign-ins.md)日志提供有关托管应用程序和用户登录活动的使用情况的信息。

你可以使用 "AzureADAuditSignInLogs cmdlet 获取登录日志的访问权限。


| 应用场景                      | PowerShell 命令 |
| :--                           | :--                |
| 用户显示名称             | AzureADAuditSignInLogs-Filter "Event.pushnotification.userdisplayname eq ' Timothy Perkins '" |
| 创建日期时间              | AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30: 00.0 Z" (自4/18 起, 5:30 pm) |
| 状态                        | AzureADAuditSignInLogs-Filter "status/errorCode eq 50105" |
| 应用程序显示名称      | AzureADAuditSignInLogs-Filter "appDisplayName eq ' StoreFrontStudio [wsfed enabled]" " |
| 以上都是              | AzureADAuditSignInLogs-Filter "Event.pushnotification.userdisplayname eq ' Timothy Perkins ' and status/errorCode ne 0 and appDisplayName eq ' StoreFrontStudio [wsfed enabled]" " |


下图显示了此命令的示例。 

![“数据摘要”按钮](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>后续步骤

- [Azure AD 报告概述](overview-reports.md)。
- [审核日志报告](concept-audit-logs.md)。 
- [以编程方式访问 Azure AD 报告](concept-reporting-api.md)
