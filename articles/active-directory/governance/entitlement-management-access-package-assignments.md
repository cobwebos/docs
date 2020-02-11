---
title: 查看、添加和删除 Azure AD 权利管理中的访问包的分配-Azure Active Directory
description: 了解如何在 Azure Active Directory 权限管理中查看、添加和删除访问包的分配。
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
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120429"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中查看、添加和删除访问包的分配

在 Azure AD 授权管理中，你可以看到已分配了哪些人员来访问包、其策略和状态。 如果访问包具有适当的策略，还可以直接将用户分配到访问包。 本文介绍如何查看、添加和删除访问包的分配。

## <a name="view-who-has-an-assignment"></a>查看谁有分配

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 单击 "**分配**" 查看活动分配列表。

    ![访问包的分配列表](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 单击特定分配查看其他详细信息。

1. 若要查看未正确预配所有资源角色的分配的列表，请单击筛选器状态，然后选择 "**传递**"。

    可以通过在**请求**页上查找用户的相应请求来查看有关传递错误的其他详细信息。

1. 若要查看过期的分配，请单击筛选器状态，然后选择 "**过期**"。

1. 若要下载筛选列表的 CSV 文件，请单击 "**下载**"。

### <a name="viewing-assignments-programmatically"></a>以编程方式查看分配

你还可以使用 Microsoft Graph 在访问包中检索分配。  适当角色中具有委托 `EntitlementManagement.ReadWrite.All` 权限的用户可以调用 API 来[列出 accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接分配用户

在某些情况下，你可能希望将特定用户直接分配到访问包，以便用户无需完成请求访问包的过程。 若要直接分配用户，访问包必须具有允许管理员直接分配的策略。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在左侧菜单中，单击 "**分配**"。

1. 单击 "**新建分配**"，打开 "添加用户到 access 包"。

    ![分配-将用户添加到 access 包](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 单击 "**添加用户**" 以选择要向其分配此访问包的用户。

1. 在 "**选择策略**" 列表中，选择将由用户的未来请求和生命周期控制和跟踪的策略。 如果你希望所选用户具有不同的策略设置，你可以单击 "**创建新策略**" 来添加新策略。

1. 设置所选用户要开始和结束的日期和时间。 如果未提供结束日期，则将使用该策略的生命周期设置。

1. （可选）为保留记录的直接分配提供理由。

1. 单击 "**添加**" 以将所选用户直接分配到访问包。

    几分钟后，单击 "**刷新**" 以查看 "分配" 列表中的用户。

### <a name="directly-assigning-users-programmatically"></a>直接以编程方式分配用户

你还可以使用 Microsoft Graph 直接将用户分配到访问包。  具有具有委托 `EntitlementManagement.ReadWrite.All` 权限的应用程序的相应角色中的用户可以调用 API，以[创建 accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>删除分配

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在左侧菜单中，单击 "**分配**"。
 
1. 单击要删除其分配的用户旁边的复选框。 

1. 单击左侧窗格顶部附近的 "**删除**" 按钮。 
 
    ![分配-从 access 包中删除用户](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    将显示一条通知，告知你分配已删除。 

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和设置](entitlement-management-access-package-request-policy.md)
- [查看报表和日志](entitlement-management-reports.md)
