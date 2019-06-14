---
title: 在 Azure AD 授权管理 （预览版）-Azure Active Directory 中查看报告和日志
description: 了解如何查看用户分配报表和审核日志在 Azure Active Directory 权利管理 （预览版）。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541535"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>查看报表和 Azure AD 授权管理 （预览版） 中的日志

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="view-resources-a-user-has-access-to"></a>查看用户有权访问的资源

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**用户分配报表**。

1. 单击**选择用户**以打开选择用户窗格。

1. 你想要查看他们有权访问的资源的列表中找到的用户。

1. 单击用户，然后单击**选择**。

    显示用户有权访问的资源的列表。 它包括访问包、 策略和日期。

    ![用户分配报表](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>确定用户的请求的状态

若要获取用户如何请求和接收权访问包的其他详细信息，可以使用 Azure AD 审核日志。 具体而言，可以使用中的日志记录`EntitlementManagement`和`UserManagement`类别为每个请求上的处理步骤获取其他详细信息。  

1. 单击**Azure Active Directory** ，然后单击**审核日志**。

1. 在顶部，更改**类别**至任一`EntitlementManagement`或`UserManagement`，取决于所需的审核记录。  

1. 单击“应用”  。

1. 若要下载日志，请单击**下载**。

当 Azure AD 收到新请求时，它将审核记录，在其中**类别**是`EntitlementManagement`并且**活动**通常是`User requests access package assignment`。  在 Azure 门户中创建一个直接分配的情况下**活动**审核记录的字段是`Administrator directly assigns user to access package`，并由标识执行分配的用户**ActorUserPrincipalName**.

Azure AD 将写入其他审核记录，当请求正在进行，包括：

| 类别 | activities | 请求状态 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 请求不需要审批 |
| `UserManagement` | `Create request approval` | 请求需要审批 |
| `UserManagement` | `Add approver to request approval` | 请求需要审批 |
| `EntitlementManagement` | `Approve access package assignment request` | 请求批准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |请求获得批准，或不需要审批 |

Azure AD 用户分配访问权限后，写入的审核记录`EntitlementManagement`使用类别**活动** `Fulfill access package assignment`。  收到的访问权限的用户由**ActorUserPrincipalName**字段。

如果不分配访问权限，则 Azure AD 写入的审核记录`EntitlementManagement`使用类别**活动**任一`Deny access package assignment request`，如果请求被拒绝的审批者或`Access package assignment request timed out (no approver action taken)`，如果请求之前已超时无法批准审批者。

用户的访问权限包分配过期时，用户已取消或删除管理员，然后 Azure AD 将写入的审核记录`EntitlementManagement`使用类别**活动**的`Remove access package assignment`。

## <a name="next-steps"></a>后续步骤

- [Azure AD 授权管理故障排除](entitlement-management-troubleshoot.md)
- [常见方案](entitlement-management-scenarios.md)
