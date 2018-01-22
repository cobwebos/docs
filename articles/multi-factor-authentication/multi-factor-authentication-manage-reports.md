---
title: "用于 Azure MFA 的访问和使用情况报告 | Microsoft Docs"
description: "介绍如何使用 Azure 多重身份验证功能 - 报告。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告

Azure 多重身份验证提供了几个可通过 Azure 门户访问的报告，供你和你的组织使用。 下表列出了可用的报告：

| 报表 | Location | 说明 |
|:--- |:--- |:--- |
| 阻止的用户历史记录 | Azure AD > MFA 服务器 > 阻止/解除阻止用户 | 显示请求阻止或解除阻止用户的历史记录。 |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |
| 本地组件的使用情况 | Azure AD > MFA 服务器 > 活动报告 | 提供有关通过 NPS 扩展、ADFS 和 MFA 服务器实现的 MFA 的总体使用情况信息。 |
| 跳过的用户历史记录 | Azure AD > MFA 服务器 > 免验证一次 | 提供请求对用户跳过多重身份验证的历史记录。 |
| 服务器状态 | Azure AD > MFA 服务器 > 服务器状态 | 显示与帐户关联的多重身份验证服务器的状态。 |

## <a name="view-reports"></a>查看报告 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧选择“Azure Active Directory” > “MFA 服务器”。
3. 选择要查看的报告。

   <center>![云](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell 报告

使用后面的 PowerShell 标识已注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

使用后面的 PowerShell 标识未注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>后续步骤

* [面向用户](end-user/multi-factor-authentication-end-user.md)
* [部署位置](multi-factor-authentication-get-started.md)
