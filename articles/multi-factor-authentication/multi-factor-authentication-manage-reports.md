---
title: "在云中查看 Azure MFA 的访问报告 | Microsoft Docs"
description: "介绍如何使用 Azure 多重身份验证功能 - 报告。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 27ff53646992308d574dcc2e631cd63b8227f9c8
ms.openlocfilehash: 8f18c3850f60795d2cdf498ca3882f49d4986c59


---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告
Azure 多重身份验证提供了几个报告供你和你的组织使用。 可通过多重身份验证管理门户访问这些报告，这需要你提供 Azure MFA 提供程序、Azure MFA、Azure AD Premium 或 Enterprise Mobility Suite 许可证。 以下是可用报告的列表。

你可以通过 Azure 管理门户访问报告。

| 名称 | 说明 |
|:--- |:--- |
| 使用情况 |使用情况报告显示有关总体使用情况的信息、用户摘要和用户详细信息。 |
| 服务器状态 |此报告显示与帐户关联的 多重身份验证服务器的状态。 |
| 阻止的用户历史记录 |这些报告显示阻止或取消阻止用户的请求的历史记录。 |
| 跳过的用户历史记录 |显示对用户的电话号码跳过 多重身份验证的请求的历史记录。 |
| 欺诈警报 |显示在指定日期范围内提交的欺诈警报的历史记录。 |
| 已排队 |列出排队等待处理的报告及其状态。 报告完成后，将提供下载或查看报告的链接。 |

## <a name="to-view-reports"></a>查看报告
1. 登录 http://azure.microsoft.com
2. 在左侧选择“Active Directory”。
3. 选择以下选项之一：
   * **选项 1**：单击“多重身份验证提供程序”选项卡。 选择你的 MFA 提供程序，然后在底部单击“管理”按钮。
   * **选项 2**：选择目录，然后单击“配置”选项卡。 在“多重身份验证”部分下，单击“管理服务设置”。 在“MFA 服务设置”页底部，单击“转到门户”链接。
4. Azure 多重身份验证管理门户上的左侧导航栏中会显示“查看报告”部分。 在这里，你可以选择上述报告。

<center>![云](./media/multi-factor-authentication-manage-reports/report.png)</center>


**其他资源**

* [面向用户](end-user/multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure 多重身份验证](https://msdn.microsoft.com/library/azure/dn249471.aspx)



<!--HONumber=Jan17_HO4-->


