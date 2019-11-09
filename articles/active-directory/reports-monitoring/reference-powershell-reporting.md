---
title: 用于报告的 Azure AD PowerShell cmdlet | Microsoft Docs
description: 用于报告的 Azure AD PowerShell cmdlet 的参考。
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
ms.openlocfilehash: dd1d4f86b6223abd3de8fab618e5975ebd33f892
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885276"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>用于报告的 Azure AD PowerShell cmdlet

> [!NOTE] 
> 这些 Powershell cmdlet 目前仅适用于 [Azure AD 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing)模块。 请注意，预览模块不建议用于生产用途。 

通过 Azure Active Directory (Azure AD) 报告，可以详细了解在说明（审核日志）和身份验证数据（登录日志）中围绕所有写入操作进行的活动。 虽然可以使用 MS Graph API 来获取信息，但现在可以使用用于报告的 Azure AD PowerShell cmdlet 来检索相同的数据。

本文概述用于审核日志和登录日志的 PowerShell cmdlet。

## <a name="audit-logs"></a>审核日志

[审核日志](concept-audit-logs.md)通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

可以使用 `Get-AzureADAuditDirectoryLogs cmdlet 访问审核日志。


| 方案                      | PowerShell 命令 |
| :--                           | :--                |
| 应用程序显示名称      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| 类别                      | Get-AzureADAuditDirectoryLogs -Filter "category eq 'Application Management'" |
| 活动日期时间            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| 以上都是              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' and category eq 'Application Management' and activityDateTime gt 2019-04-18"|


下图显示了此命令的一个示例。 

![“数据摘要”按钮](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>登录日志

[登录](concept-sign-ins.md)日志提供有关托管应用程序的使用情况和用户登录活动的信息。

可以使用 `Get-AzureADAuditSignInLogs cmdlet 访问登录日志。


| 方案                      | PowerShell 命令 |
| :--                           | :--                |
| 用户显示名称             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| 创建日期时间              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z"（自 4 月 18 日下午 5:30 以来的一切） |
| 状态                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| 应用程序显示名称      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| 以上都是              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' and status/errorCode ne 0 and appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


下图显示了此命令的一个示例。 

![“数据摘要”按钮](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>后续步骤

- [Azure AD 报告概述](overview-reports.md)。
- [审核日志报告](concept-audit-logs.md)。 
- [以编程方式访问 Azure AD 报告](concept-reporting-api.md)
