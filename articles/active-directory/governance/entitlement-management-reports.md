---
title: 在授权管理中查看报表 & 日志-Azure AD
description: 了解如何在 Azure Active Directory 授权管理中查看 "用户分配" 报表和 "审核日志"。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128417"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>查看 Azure AD 权利管理中的报告和日志

Azure AD 的权利管理报表和 Azure AD 审核日志提供了用户有权访问的资源的其他详细信息。 作为管理员，你可以查看用户的访问包和资源分配，并查看请求日志以供审核，或确定用户请求的状态。 本文介绍如何使用权利管理报表和 Azure AD 审核日志。

观看以下视频，了解如何在 "权利管理" 中查看用户有权访问的资源：

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>查看用户的访问包

使用此报告，可以列出用户可以请求的所有访问包和当前分配给用户的访问包。

**必备角色：** 全局管理员或用户管理员

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击 "**报表**"。

1. 单击 "**访问用户的包**"。

1. 单击“选择用户”，打开“选择用户”窗格****。

1. 在列表中查找用户，然后单击 "**选择**"。

    "**可以请求**" 选项卡显示用户可以请求的访问包的列表。 此列表由为访问包定义的[请求策略](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)确定。 

    ![访问用户的包](./media/entitlement-management-reports/access-packages-report.png)

1. 如果访问包有多个资源角色或策略，请单击 "资源角色或策略" 条目以查看选择详细信息。

1. 单击 "**分配**" 选项卡以查看当前分配给用户的访问包的列表。 向用户分配访问包时，这意味着用户有权访问访问包中的所有资源角色。

## <a name="view-resource-assignments-for-a-user"></a>查看用户的资源分配

使用此报告可以列出当前分配给授权管理的用户的资源。 请注意，此报表适用于通过授权管理管理的资源。 用户可能有权访问权限管理之外的目录中的其他资源。

**必备角色：** 全局管理员或用户管理员

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击 "**报表**"。

1. 单击 "**资源分配**"。

1. 单击“选择用户”，打开“选择用户”窗格****。

1. 在列表中查找用户，然后单击 "**选择**"。

    将显示当前分配给用户的资源列表。 此列表还显示了访问包及其从其获取资源角色的策略，以及访问的开始和结束日期。
    
    如果用户可以访问两个或更多个包中的同一资源，则可以单击箭头查看每个包和策略。

    ![用户的资源分配](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>确定用户的请求的状态

若要更详细地了解用户如何请求访问某个访问包，以及如何接受对该包的访问，可以使用 Azure AD 审核日志。 具体说来，可以使用`EntitlementManagement` 和 `UserManagement` 类别中的日志记录来更详细地了解每个请求的处理步骤。  

1. 单击“Azure Active Directory”，然后单击“审核日志”。********

1. 在顶部将“类别”更改为****`EntitlementManagement` 或 `UserManagement`，具体取决于要查找的审核记录。  

1. 单击“应用”  。

1. 若要下载日志，请单击“下载”。****

Azure AD 在收到新请求时会写入一条审核记录，其中的“类别”为****`EntitlementManagement`，“活动”通常为****`User requests access package assignment`。  如果是在 Azure 门户中创建的直接分配，则审核记录的“活动”字段**** 为 `Administrator directly assigns user to access package`，执行分配的用户通过 **ActorUserPrincipalName** 来标识。

Azure AD 会在请求正在进行时写入更多的审核记录，包括：

| 类别 | 活动 | 请求状态 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 请求不需要审批 |
| `UserManagement` | `Create request approval` | 请求需要审批 |
| `UserManagement` | `Add approver to request approval` | 请求需要审批 |
| `EntitlementManagement` | `Approve access package assignment request` | 已批准请求 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |请求已获得批准，或者不需要审批 |

为用户分配访问权限以后，Azure AD 会写入一条审核记录，其类别为 `EntitlementManagement`，其“活动”为**** `Fulfill access package assignment`。  收到访问权限的用户按 **ActorUserPrincipalName** 字段进行标识。

如果未分配访问权限，则 Azure AD 会写入一条审核记录，其类别为 `EntitlementManagement`，其“活动”为****`Deny access package assignment request`（如果请求被审批者拒绝）或 `Access package assignment request timed out (no approver action taken)`（如果请求已在审批者进行审批之前超时）。

如果用户的访问包分配过期、被用户取消，或者被管理员删除，则 Azure AD 会写入一条审核记录，其类别为 `EntitlementManagement`，其“活动”为****`Remove access package assignment`。

## <a name="next-steps"></a>后续步骤

- [存档报表和日志](entitlement-management-logs-and-reporting.md)
- [排查 Azure AD 权利管理的问题](entitlement-management-troubleshoot.md)
- [常见方案](entitlement-management-scenarios.md)
