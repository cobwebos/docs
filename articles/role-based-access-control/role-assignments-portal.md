---
title: 管理对 Azure 资源使用 RBAC 和 Azure 门户的访问 |Microsoft Docs
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
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805284"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 管理对 Azure 资源的访问权限。 本文介绍如何管理使用 Azure 门户的访问。 如果需要管理对 Azure Active Directory 访问权限，请参阅[Azure Active Directory 中的视图，并将分配管理员角色](../active-directory/users-groups-roles/directory-manage-roles-portal.md)。

## <a name="prerequisites"></a>必备组件

若要添加和删除角色分配，必须具有：

- `Microsoft.Authorization/roleAssignments/write` 并`Microsoft.Authorization/roleAssignments/delete`权限，如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>访问控制 (IAM) 概述

**访问控制 (IAM)** 是用于管理 Azure 资源的访问权限的边栏选项卡。 它是也称为标识和访问管理，并显示在 Azure 门户中的多个位置。 下面显示了订阅的“访问控制(IAM)”边栏选项卡的示例。

![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/access-control-numbers.png)

下表描述了内容的某些元素是用于：

| # | 元素 | 你将其用于 |
| --- | --- | --- |
| 第 | 其中打开访问控制 (IAM) 的资源 | 标识作用域 （在此示例中的订阅） |
| 2 | **添加**按钮 | 添加角色分配 |
| 3 | **检查访问**选项卡 | 查看单个用户的角色分配 |
| 4 | **角色分配**选项卡 | 查看当前作用域内的角色分配 |
| 5 | **角色**选项卡 | 查看所有角色和权限 |

若要最有效的访问控制 (IAM) 边栏选项卡，它有助于如果想要管理的访问权限时，可以回答以下三个问题：

1. **谁需要访问？**

    谁是指用户、 组、 服务主体或托管的标识。 这也称为*安全主体*。

1. **他们需要什么权限？**

    权限则组合到角色。 您可以从多个内置角色的列表中选择。

1. **它们在其中需要访问？**

    其中是指的访问权限适用于的资源组。 位置可以是管理组、 订阅、 资源组或对单个资源，例如存储帐户。 这称为*作用域*。

## <a name="open-access-control-iam"></a>打开访问控制 (IAM)

首先您需要决定是要在何处打开访问控制 (IAM) 边栏选项卡。 这取决于您要为哪些的资源管理的访问权限。 若要为管理组，在订阅、 资源组或单个资源中的所有内容中的所有内容中的所有管理访问权限吗？

1. 在 Azure 门户中，单击**所有服务**，然后选择作用域。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”。

    下面显示了订阅的“访问控制(IAM)”边栏选项卡的示例。 如果使任何的访问控制更改，它们将应用于整个订阅。

    ![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>查看角色和权限

角色定义是用于角色分配的权限的集合。 Azure 提供超过 70 个 [Azure 资源的内置角色](built-in-roles.md)。 按照以下步骤，若要查看可用的角色和权限。

1. 打开**访问控制 (IAM)** 在任何范围内。

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表。

   您可以看到多个用户和组分配给当前范围内的每个角色。

   ![角色列表](./media/role-assignments-portal/roles-list.png)

1. 单击某个角色以查看分配到该角色的人员，还可以查看该角色的权限。

   ![角色分配](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>查看角色分配

在管理访问权限时，你想要知道谁有权访问，什么是其权限，而是在哪个范围内。 列表访问用户、 组、 服务主体或托管的标识，查看其角色分配。

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

   在“角色分配”选项卡上，可以看到谁有权访问此范围。 请注意，有些角色的权限范围已划归到**此资源**，还有一些角色是从另一范围 **(继承的)**。 访问权限可以专门分配给此资源，也可以从父作用域的分配继承。

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
* [使用 Azure 管理组来组织资源](../governance/management-groups/index.md)
