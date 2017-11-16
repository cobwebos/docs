---
title: "用于 Azure MFA 的访问和使用情况报告 | Microsoft Docs"
description: "介绍如何使用 Azure 多重身份验证功能 - 报告。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: a0ac1711b6bfb8f461cd775ed1f3409925643615
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告

Azure 多重身份验证提供了几个报告供你和组织使用。 可以通过多重身份验证管理门户访问这些报告。 下表列出了可用的报告：

| 报表 | 说明 |
|:--- |:--- |
| 使用情况 |使用情况报告显示有关总体使用情况的信息、用户摘要和用户详细信息。 |
| 服务器状态 |此报告显示与帐户关联的 多重身份验证服务器的状态。 |
| 阻止的用户历史记录 |这些报告显示阻止或取消阻止用户的请求的历史记录。 |
| 跳过的用户历史记录 |显示对用户的电话号码跳过 多重身份验证的请求的历史记录。 |
| 欺诈警报 |显示在指定日期范围内提交的欺诈警报的历史记录。 |
| 已排队 |列出排队等待处理的报告及其状态。 报告完成后，将提供下载或查看报告的链接。 |

## <a name="view-reports"></a>查看报告

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧选择“Active Directory”。
3. 选择这两个选项之一，具体取决于是否使用身份验证提供程序：
   * **选项 1**：单击“多重身份验证提供程序”选项卡。选择 MFA 提供程序，并在底部单击“管理”按钮。
   * **选项 2**：选择目录，并转到“配置”选项卡。在“多重身份验证”部分下，选择“管理服务设置”。 在“MFA 服务设置”页底部，单击“转到门户”链接。
4. 在 Azure 多重身份验证管理门户中，从左侧导航栏的“查看报告”中选择所需的报告类型。

<center>![云](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Powershell 报告

使用后面的 Powershell 标识已注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

使用后面的 Powershell 标识未注册 MFA 的用户。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**其他资源**

* [面向用户](end-user/multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure 多重身份验证](https://msdn.microsoft.com/library/azure/dn249471.aspx)
