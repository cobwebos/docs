---
title: 使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 Azure 门户来管理用户、组、服务主体和托管标识对 Azure 资源的访问权限。 这包括如何列出访问权限、授予访问权限以及删除访问权限。
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337625"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 管理对 Azure 资源的访问权限。 本文介绍如何使用 Azure 门户管理访问权限。 如需管理对 Azure Active Directory 的访问权限，请参阅[在 Azure Active Directory 中查看和分配管理员角色](../active-directory/users-groups-roles/directory-manage-roles-portal.md)。

## <a name="prerequisites"></a>先决条件

若要添加和删除角色分配，必须具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>“访问控制(IAM)”概述

使用“访问控制(IAM)”边栏选项卡可以管理对 Azure 资源的访问权限。 该功能也称为标识和访问管理，会显示在 Azure 门户中的多个位置。 下面显示了订阅的“访问控制(IAM)”边栏选项卡的示例。

![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/access-control-numbers.png)

下表描述了一些元素的用途：

| # | 元素 | 用途 |
| --- | --- | --- |
| 1 | 在其中打开访问控制 (IAM) 的资源 | 标识范围（在本示例中为订阅） |
| 2 | “添加”按钮 | 添加角色分配 |
| 3 | “检查访问权限”选项卡 | 查看单个用户的角色分配 |
| 4 | “角色分配”选项卡 | 查看当前范围的角色分配 |
| 5 | “角色”选项卡 | 查看所有角色和权限 |

如果在尝试管理访问权限时能够回答以下三个问题，则可以最有效地利用“访问控制(IAM)”边栏选项卡：

1. **谁需要访问权限？**

    引用用户、组、服务主体或托管标识的用户。 这也称为*安全主体*。

1. **他们需要哪些权限？**

    权限组合成角色。 可以从多个内置角色的列表中选择。

1. **他们在何处需要访问权限？**

    “何处”是指访问权限应用到的资源集。 “何处”可以是管理组、订阅、资源组或单个资源，例如存储帐户。 这称为“范围”。

## <a name="open-access-control-iam"></a>打开访问控制 (IAM)

需要确定的第一件事是在何处打开“访问控制(IAM)”边栏选项卡。 这取决于要管理哪些资源的访问权限。 是要管理管理组中所有对象、订阅中所有对象、资源组中所有对象还是单个资源的访问权限？

1. 在 Azure 门户中单击“所有服务”，然后选择范围。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”。

    下面显示了订阅的“访问控制(IAM)”边栏选项卡的示例。 如果在此处进行任何访问控制更改，这些更改将应用到整个订阅。

    ![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>查看角色和权限

角色定义是用于角色分配的权限的集合。 Azure 提供超过 70 个 [Azure 资源的内置角色](built-in-roles.md)。 遵循以下步骤查看可用的角色和权限。

1. 在任一范围打开“访问控制(IAM)”。

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表。

   可以看到在当前范围分配到每个角色的用户和组的数目。

   ![角色列表](./media/role-assignments-portal/roles-list.png)

1. 单击某个角色以查看分配到该角色的人员，还可以查看该角色的权限。

   ![角色分配](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>查看角色分配

管理访问权限时，需要了解谁拥有访问权限、其权限是什么，以及权限范围是什么。 若要列出某个用户、组、服务主体或托管标识的访问权限，请查看其角色分配。

### <a name="view-role-assignments-for-a-single-user"></a>查看单个用户的角色分配

请按照以下步骤查看特定范围内单个用户、组、服务主体或托管标识的访问权限。

1. 在要查看权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制(IAM)”。

1. 单击“访问权限检查”选项卡。

    ![“访问控制”-“检查访问权限”选项卡](./media/role-assignments-portal/access-control-check-access.png)

1. 在“查找”列表中，选择要检查访问权限的安全主体类型。

1. 在搜索框中，输入字符串以在目录中搜索显示名称、电子邮件地址或对象标识符。

    ![“检查访问权限”选择列表](./media/role-assignments-portal/check-access-select.png)

1. 单击安全主体以打开“分配”窗格。

    ![分配窗格](./media/role-assignments-portal/check-access-assignments.png)

    在此窗格中，可以看到分配给所选安全主体和范围的角色。 如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

### <a name="view-all-role-assignments-at-a-scope"></a>查看某个范围内的所有角色分配

1. 在要查看权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配。

   ![“访问控制”-“角色分配”选项卡](./media/role-assignments-portal/access-control-role-assignments.png)

   在“角色分配”选项卡上，可以看到谁有权访问此范围。 请注意，有些角色的权限范围已划归到**此资源**，还有一些角色是从另一范围 **(继承的)** 。 访问权限可以专门分配给此资源，也可以从父作用域的分配继承。

## <a name="add-a-role-assignment"></a>添加角色分配

在 RBAC 中，要授予访问权限，请为用户、组、服务主体或托管标识分配角色。 通过以下步骤在不同的范围授予访问权限。

### <a name="assign-a-role-at-a-scope"></a>在范围内分配角色

1. 在要授予访问权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制 (IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配。

1. 单击“添加” > “添加角色分配”以打开“添加角色分配”窗格。

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![添加菜单](./media/role-assignments-portal/add-menu.png)

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。

1. 在“选择”列表中，选择用户、组、服务主体或托管标识。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 单击“保存”以分配该角色。

   片刻之后，会在所选范围内为安全主体分配角色。

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>将用户分配为订阅的管理员

要使用户成为 Azure 订阅的管理员，请在订阅范围为其分配[所有者](built-in-roles.md#owner)角色。 “所有者”角色会为该用户提供订阅中所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 这些步骤与任何其他角色分配相同。

1. 在 Azure 门户中，依次单击“所有服务”、“订阅”。

1. 单击要授予访问权限的订阅。

1. 单击“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看此订阅的所有角色分配。

1. 单击“添加” > “添加角色分配”以打开“添加角色分配”窗格。

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![添加菜单](./media/role-assignments-portal/add-menu.png)

   ![“添加角色分配”窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在“角色”下拉列表中，选择“所有者”角色。

1. 在“选择”列表中，选择用户。 如果没有在列表中看到用户，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称和电子邮件地址。

1. 单击“保存”以分配该角色。

   片刻之后，会在订阅范围内为该用户分配“所有者”角色。

## <a name="remove-role-assignments"></a>删除角色分配

在 RBAC 中，若要删除访问权限，请删除角色分配。 按这些步骤删除访问权限。

1. 在要删除访问权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看此订阅的所有角色分配。

1. 在角色分配列表中，在需删除其角色分配的安全主体旁边添加复选标记。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-select.png)

1. 单击“删除”。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。

    不能删除继承的角色分配。 如果需要删除继承的角色分配，则必须在创建角色分配的作用域上进行操作。 在“作用域”列的“(继承)”旁，有一条链接指向分配了此角色的范围。 请转到该处列出的范围以删除角色分配。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>后续步骤

* [教程：使用 RBAC 和 Azure 门户授予用户对 Azure 资源的访问权限](quickstart-assign-role-user-portal.md)
* [教程：使用 RBAC 和 Azure PowerShell 授予用户对 Azure 资源的访问权限](tutorial-role-assignments-user-powershell.md)
* [对 Azure 资源的 RBAC 进行故障排除](troubleshooting.md)
* [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)
