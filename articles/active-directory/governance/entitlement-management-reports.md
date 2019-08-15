---
title: 查看 Azure AD 权利管理 (预览版) 中的报告和日志-Azure Active Directory
description: 了解如何在 Azure Active Directory 授权管理 (预览版) 中查看 "用户分配" 报表和 "审核日志"。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032438"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>查看 Azure AD 授权管理 (预览版) 中的报告和日志

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

"用户分配" 报表和 Azure Active Directory 审核日志提供了有关目录中用户的其他详细信息。 作为管理员, 你可以查看用户有权访问的资源并查看请求日志以供审核, 或确定用户请求的状态。 本文介绍如何使用 "用户分配" 报表和 Azure AD 审核日志。

观看以下视频, 了解如何使用权利管理来管理 Azure Active Directory 中的用户的访问权限:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>查看用户有权访问的资源

1. 依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**用户分配报表**"。

1. 单击 "**选择用户**" 打开 "选择用户" 窗格。

1. 在列表中找到要查看其有权访问的资源的用户。

1. 单击该用户, 然后单击 "**选择**"。

    将显示用户有权访问的资源列表。 它包括访问包、策略和日期。

    ![用户分配报表](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>确定用户请求的状态

若要获取有关用户请求和接收访问包访问权限的其他详细信息, 可以使用 Azure AD 审核日志。 具体而言, 你可以使用`EntitlementManagement`和`UserManagement`类别中的日志记录来获取有关每个请求的处理步骤的更多详细信息。  

1. 单击**Azure Active Directory** , 然后单击 "**审核日志**"。

1. 在顶部, 将**类别**更改为`EntitlementManagement`或`UserManagement`, 具体取决于所查找的审核记录。  

1. 单击“应用”。

1. 若要下载日志, 请单击 "**下载**"。

当 Azure AD 收到新请求时, 它会写入一个审核记录, 其中**类别** `EntitlementManagement`为, 而**活动**通常`User requests access package assignment`为。  如果在 Azure 门户中创建了直接分配, 则审核记录的**活动**字段为`Administrator directly assigns user to access package`, 执行分配的用户将由**ActorUserPrincipalName**标识。

Azure AD 将在请求进行时写入其他审核记录, 其中包括:

| 类别 | activities | 请求状态 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 请求不需要审批 |
| `UserManagement` | `Create request approval` | 请求需要批准 |
| `UserManagement` | `Add approver to request approval` | 请求需要批准 |
| `EntitlementManagement` | `Approve access package assignment request` | 请求已批准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |请求已批准, 或不需要审批 |

向用户分配访问权限时, Azure AD 会写入包含`EntitlementManagement` **活动** `Fulfill access package assignment`的类别的审核记录。  接收访问权限的用户通过**ActorUserPrincipalName**字段进行标识。

如果未分配访问权限, 则 Azure AD 会写入包含`EntitlementManagement` **活动** `Deny access package assignment request`的类别的审核记录, 如果请求被审批者拒绝, 则`Access package assignment request timed out (no approver action taken)`为; 如果请求在审批者批准之前已超时, 则为。

如果用户的访问包分配过期、被用户取消或被管理员删除, 则 Azure AD 为`EntitlementManagement` **活动**的`Remove access package assignment`类别写入审核记录。

## <a name="next-steps"></a>后续步骤

- [Azure AD 权限管理疑难解答](entitlement-management-troubleshoot.md)
- [常见方案](entitlement-management-scenarios.md)
