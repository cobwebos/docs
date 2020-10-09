---
title: 在权利管理中查看报表和日志 - Azure AD
description: 了解如何在 Azure Active Directory 权利管理中查看用户分配报表和审核日志。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f2d5202a9b5439fcacca549659e4e181ffeca4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85078137"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中查看报表和日志

Azure AD 权利管理报表和 Azure AD 审核日志提供有关用户对哪些资源具有访问权限的更多详细信息。 作为管理员，你可以查看用户的访问包和资源分配，并且可以查看请求日志以便进行审核或确定用户请求的状态。 本文介绍如何使用权利管理报表和 Azure AD 审核日志。

观看以下视频，了解如何在 "权利管理" 中查看用户有权访问的资源：

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>查看用户的访问包

使用此报表，可以列出用户能够请求的所有访问包以及当前分配给该用户的访问包。

**必备角色：** 全局管理员或用户管理员

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击“报表”。

1. 单击“用户的访问包”。

1. 单击“选择用户”，打开“选择用户”窗格****。

1. 在列表中查找该用户，然后单击“选择”。

    “可以请求”选项卡显示该用户可以请求的访问包的列表。 此列表由为访问包定义的[请求策略](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)确定。 

    ![用户的访问包](./media/entitlement-management-reports/access-packages-report.png)

1. 如果某个访问包有多个资源角色或策略，请单击“资源角色”或“策略”条目，以查看所选项详细信息。

1. 单击“已分配”选项卡，以查看当前已分配给该用户的访问包的列表。 向用户分配了访问包后，这就意味着该用户有权访问此访问包中所有的资源角色。

## <a name="view-resource-assignments-for-a-user"></a>查看用户的资源分配

使用此报表可以列出权利管理中当前分配给用户的资源。 请注意，此报表适用于利用权利管理进行管理的资源。 用户可能有权访问权利管理之外的目录中的其他资源。

**必备角色：** 全局管理员或用户管理员

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击“报表”。

1. 单击“用户的资源分配”。

1. 单击“选择用户”，打开“选择用户”窗格****。

1. 在列表中查找该用户，然后单击“选择”。

    这时会显示当前分配给该用户的资源列表。 此列表还显示这些资源从中获取资源角色的访问包和策略，同时还显示访问权限的开始和结束日期。
    
    如果用户在两个或更多的包中获得了对同一资源的访问权限，你可以单击箭头来查看各个包和策略。

    ![用户的资源分配](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>确定用户的请求的状态

若要更详细地了解用户如何请求访问某个访问包，以及如何接受对该包的访问，可以使用 Azure AD 审核日志。 具体说来，可以使用`EntitlementManagement` 和 `UserManagement` 类别中的日志记录来更详细地了解每个请求的处理步骤。  

1. 单击“Azure Active Directory”，然后单击“审核日志”。********

1. 在顶部将“类别”更改为****`EntitlementManagement` 或 `UserManagement`，具体取决于要查找的审核记录。  

1. 单击“应用” 。

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

- [将报表和日志存档](entitlement-management-logs-and-reporting.md)
- [排查 Azure AD 权利管理的问题](entitlement-management-troubleshoot.md)
- [常见方案](entitlement-management-scenarios.md)
