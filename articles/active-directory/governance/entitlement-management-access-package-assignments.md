---
title: 在 Azure AD 授权管理中查看、添加和删除访问包的分配 - Azure 活动目录
description: 了解如何在 Azure 活动目录授权管理中查看、添加和删除访问包的分配。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262017"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中查看、添加和删除访问包的分配

在 Azure AD 授权管理中，您可以看到已分配到访问包的人员、策略和状态。 如果访问包具有适当的策略，也可以直接将用户分配给访问包。 本文介绍如何查看、添加和删除访问包的分配。

## <a name="view-who-has-an-assignment"></a>查看谁具有分配

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“分配”查看活动分配的列表。****

    ![访问包的分配列表](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 单击特定的分配可查看更多详细信息。

1. 若要查看并非所有资源角色都已未正确预配的分配列表，请单击筛选器状态，然后选择“正在传递”。****

    在“请求”页上找到用户的对应请求，可以查看有关传递错误的更多详细信息。****

1. 若要查看已过期的分配，请单击筛选器状态，然后选择“已过期”。****

1. 若要下载筛选列表的 CSV 文件，请单击“下载”。****

### <a name="viewing-assignments-programmatically"></a>以编程方式查看分配

您还可以使用 Microsoft 图形检索访问包中的分配。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以调用 API 来[列出访问包分配](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接分配用户

在某些情况下，你可能希望直接将特定的用户分配到访问包，使其不必要完成请求访问包的过程。 若要直接分配用户，访问包必须有一个允许管理员直接分配的策略。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在左侧菜单中单击“分配”。****

1. 单击“新建分配”打开“将用户添加到访问包”。****

    ![分配 - 将用户添加到访问包](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 单击"**添加用户**"以选择要为此访问包分配给的用户。

1. 在 **"选择策略**"列表中，选择用户未来请求和生命周期将由该策略进行管理和跟踪的策略。 如果希望所选用户具有不同的策略设置，可以单击 **"创建新策略**"以添加新策略。

1. 设置所选用户的分配开始与结束日期和时间。 如果未提供结束日期，将使用策略的生命周期设置。

1. （可选）为直接分配提供理由，以保留记录。

1. 单击“添加”将所选用户直接分配到访问包。****

    片刻之后，单击“刷新”查看“分配”列表中的用户。****

### <a name="directly-assigning-users-programmatically"></a>以编程方式直接分配用户

您还可以使用 Microsoft 图形将用户直接分配给访问包。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以调用 API[以创建访问包分配请求](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>删除分配

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在左侧菜单中单击“分配”。****
 
1. 单击要从访问包中删除其分配的用户旁边的复选框。 

1. 单击左侧窗格顶部附近的 **"删除**"按钮。 
 
    ![分配 - 从访问包中删除用户](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    将显示一条通知，通知您已删除分配。 

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和设置](entitlement-management-access-package-request-policy.md)
- [查看报表和日志](entitlement-management-reports.md)
