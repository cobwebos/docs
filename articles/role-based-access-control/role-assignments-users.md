---
title: 查看 Azure 资源访问权限分配 | Microsoft Docs
description: 在 Azure 门户中查看和管理任何用户或组的所有基于角色的访问控制分配
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: rolyon
ms.openlocfilehash: 94662c99ace749f50deccf4c8cbcd9d42fee75e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>在 Azure 门户中查看用户和组的访问权限分配
> [!div class="op_single_selector"]
> * [管理用户或组的访问权限](role-assignments-users.md)
> * [管理资源的访问权限](role-assignments-portal.md)

借助 Azure Active Directory (Azure AD) 中基于角色的访问控制 (RBAC)，可以管理对 Azure 资源的访问权限。 

使用 RBAC 分配的访问权限非常精准，因为有两种方法可供你限制权限：

* **作用域：** RBAC 角色分配的作用域为特定订阅、资源组或资源。 授予对单个资源访问权限的用户无法访问同一订阅中的任何其他资源。
* **角色：** 在分配范围内，通过分配角色可进一步收窄访问权限。 角色可以是高级别的（如所有者），也可以是特定的（如虚拟机阅读器）。

只可以在订阅、资源组或资源（即分配范围）内分配角色。 不过可以在单个位置查看给定用户或组的所有访问权限分配。 每个订阅中最多可以拥有 2000 个角色分配。 

获取有关如何[使用角色分配管理对 Azure 订阅资源的访问权限](role-assignments-portal.md)的详细信息。

## <a name="view-access-assignments"></a>查看访问权限分配
若要查找单个用户或组的访问权限分配，请从 [Azure 门户](http://portal.azure.com)中的 Azure Active Directory 开始。

1. 选择“Azure Active Directory”。 如果此选项在导航列表中不可见，请选择“所有服务”，然后向下滚动即可找到“Azure Active Directory”。
2. 选择“用户和组”，并选择“所有用户”或“所有组”。 在本例中，我们侧重于个人用户。
    ![在 Azure Active Directory 中管理用户和组 - 屏幕截图](./media/role-assignments-users/rbac_users_groups.png)
3. 按名称或用户名搜索用户。
4. 在用户边栏选项卡上，选择“Azure 资源”。 将列出该用户的所有访问权限分配。

### <a name="read-permissions-to-view-assignments"></a>查看分配的读取权限
本页仅显示你有权读取的访问权限分配。 例如，具有对订阅 A 的读取权限，并转到 Azure 资源边栏选项卡以检查用户的分配。 可以看到她的订阅 A 的访问权限分配，但看不到她还具有订阅 B 上的访问权限分配。

## <a name="delete-access-assignments"></a>删除访问权限分配
通过此边栏选项卡，可以删除已直接分配给用户或组的访问权限分配。 如果访问权限分配继承自父组，需要转到资源或订阅，并在该处管理相应的分配。

1. 从用户或组的所有访问权限分配列表中，选择一个要删除的访问权限分配。
2. 选择“删除”，并选择“是”以确认。
    ![删除访问权限分配 - 屏幕截图](./media/role-assignments-users/delete_assignment.png)

## <a name="next-steps"></a>后续步骤

* 开始使用基于角色的访问控制，以[使用角色分配来管理对 Azure 订阅资源的访问权限](role-assignments-portal.md)。
* 请参阅 [RBAC 内置角色](built-in-roles.md)

