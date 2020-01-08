---
title: 使用 Azure RBAC 和 Azure 门户列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 Azure 门户来确定用户、组、服务主体或托管标识有权访问哪些资源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c265e03cfea2ebe8bbe55a63ade04bffd06360e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462248"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 门户列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍了如何使用 Azure 门户列出角色分配。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出用户或组的角色分配

若要查看分配给订阅中的用户或组的角色，最简单的方法是使用 " **Azure 资源**" 窗格。

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择 "**用户**或**组**"。

1. 单击要列出其角色分配的用户或组。

1. 单击“Azure 资源”。

    你会在管理组、订阅、资源组或资源等不同范围内看到分配给所选用户或组的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![用户的角色分配](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 若要更改订阅，请单击 "**订阅**" 列表。

## <a name="list-role-assignments-at-a-scope"></a>列出范围内的角色分配

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源。

1. 单击特定资源。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配。

   ![“访问控制”-“角色分配”选项卡](./media/role-assignments-list-portal/access-control-role-assignments.png)

   在“角色分配”选项卡上，可以看到谁有权访问此范围。 请注意，有些角色的权限范围已划归到**此资源**，还有一些角色是从另一范围 **(继承的)** 。 访问权限可以专门分配给此资源，也可以从父作用域的分配继承。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出某个用户在某个范围内的角色分配

若要列出用户、组、服务主体或托管标识的访问权限，请列出其角色分配。 按照以下步骤列出特定范围内单个用户、组、服务主体或托管标识的角色分配。

1. 在 Azure 门户中，单击 "**所有服务**"，然后选择范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源。

1. 单击特定资源。

1. 单击“访问控制(IAM)”。

1. 单击“检查访问权限”选项卡。

    ![“访问控制”-“检查访问权限”选项卡](./media/role-assignments-list-portal/access-control-check-access.png)

1. 在“查找”列表中，选择要检查访问权限的安全主体类型。

1. 在搜索框中，输入字符串以在目录中搜索显示名称、电子邮件地址或对象标识符。

    ![“检查访问权限”选择列表](./media/role-assignments-list-portal/check-access-select.png)

1. 单击安全主体以打开“分配”窗格。

    ![分配窗格](./media/role-assignments-list-portal/check-access-assignments.png)

    在此窗格中，可以看到分配给所选安全主体和范围的角色。 如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>为系统分配的托管标识列出角色分配

1. 在 Azure 门户中，打开一个系统分配的托管标识。

1. 在左侧菜单中，单击 "**标识**"。

    ![系统分配的托管标识](./media/role-assignments-list-portal/identity-system-assigned.png)

1. 在 "**角色分配**" 下，单击 **"显示分配给此托管标识的 Azure RBAC 角色"** 。

    在管理组、订阅、资源组或资源等不同范围内，你会看到分配给所选系统分配的托管标识的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>列出用户分配的托管标识的角色分配

1. 在 Azure 门户中，打开一个系统分配的托管标识。

1. 单击“Azure 资源”。

    在管理组、订阅、资源组或资源等不同范围内，你会看到分配给所选用户分配的托管标识的角色的列表。 此列表包含您有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 若要更改订阅，请单击 "**订阅**" 列表。

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 Azure 门户添加或删除角色分配](role-assignments-portal.md)
- [ Azure 资源 RBAC 故障排除](troubleshooting.md)
