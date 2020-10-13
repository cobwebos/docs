---
title: 在 Azure AD 权利管理中查看、添加和删除访问包的分配 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理中查看、添加和删除访问包的分配。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980064"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中查看、添加和删除访问包的分配

在 Azure AD 权利管理中，可以看到哪一用户已分配到访问包，这些包的策略以及状态。 如果访问包具有相应的策略，也可以直接将用户分配到访问包。 本文介绍如何查看、添加和删除访问包的分配。

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理并将用户分配到访问包，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="view-who-has-an-assignment"></a>查看谁具有分配

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理器或访问包分配管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“分配”查看活动分配的列表。****

    ![访问包分配情况的列表](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 单击特定的分配可查看更多详细信息。

1. 若要查看并非所有资源角色都已未正确预配的分配列表，请单击筛选器状态，然后选择“正在传递”。****

    在“请求”页上找到用户的对应请求，可以查看有关传递错误的更多详细信息。****

1. 若要查看已过期的分配，请单击筛选器状态，然后选择“已过期”。****

1. 若要下载筛选列表的 CSV 文件，请单击“下载”。****

### <a name="viewing-assignments-programmatically"></a>以编程方式查看分配

你也可以使用 Microsoft Graph 在访问包中检索分配。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[列出 accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接分配用户

在某些情况下，你可能希望直接将特定的用户分配到访问包，使其不必要完成请求访问包的过程。 若要直接分配用户，访问包必须有一个允许管理员直接分配的策略。

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理器或访问包分配管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在左侧菜单中单击“分配”。****

1. 单击“新建分配”打开“将用户添加到访问包”。****

    ![分配 - 将用户添加到访问包](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 单击“添加用户”，以选择要将此访问包分配到的用户。

1. 在“选择策略”列表中，选择将会控制和跟踪用户的未来请求和生命周期的策略。 如果希望所选用户具有不同的策略设置，可单击“创建新策略”来添加新策略。

1. 设置所选用户的分配开始与结束日期和时间。 如果未提供结束日期，将使用该策略的生命周期设置。

1. （可选）为直接分配提供理由，以保留记录。

1. 单击“添加”将所选用户直接分配到访问包。****

    片刻之后，单击“刷新”查看“分配”列表中的用户。****

### <a name="directly-assigning-users-programmatically"></a>以编程方式直接分配用户

你也可以使用 Microsoft Graph 直接将用户分配到访问包。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[创建 accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>删除分配

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理器或访问包分配管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在左侧菜单中单击“分配”。****
 
1. 单击要从访问包中删除其分配的用户旁边的复选框。 

1. 单击左侧窗格顶部附近的“删除”按钮。 
 
    ![分配 - 从访问包中删除用户](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    将会显示通知，告知该分配已被删除。 

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和设置](entitlement-management-access-package-request-policy.md)
- [查看报表和日志](entitlement-management-reports.md)