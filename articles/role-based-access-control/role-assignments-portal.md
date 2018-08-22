---
title: 使用 RBAC 和 Azure 门户管理访问权限 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 Azure 门户来管理用户、组和应用程序的访问权限。 这包括如何列出访问权限、授予访问权限以及删除访问权限。
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
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 97bf33cb882d5a121b9811a8e36a1d26f9a954f8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715363"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>使用 RBAC 和 Azure 门户管理访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](overview.md) 这种方式管理对资源的访问。 本文介绍如何使用 RBAC 和 Azure 门户来管理用户、组和应用程序的访问权限。

## <a name="list-roles"></a>列出角色

角色定义是用于角色分配的权限的集合。 Azure 有 70 个以上的[内置角色](built-in-roles.md)。 在门户中按照以下步骤列出角色。

1. 在 Azure 门户中选择“所有服务”，然后选择“订阅”。

1. 选择订阅。

1. 选择“访问控制(IAM)”。

   ![“角色”选项](./media/role-assignments-portal/list-subscription-access-control.png)

1. 选择“角色”即可看到一个包含所有内置角色和自定义角色的列表。

   ![“角色”选项](./media/role-assignments-portal/roles-option.png)

   可以查看分配给每个角色的用户和组的数目。

   ![角色列表](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>列出访问权限

管理访问权限时，需了解谁有访问权限、其权限是什么，以及权限级别。 若要列出访问权限，请列出角色分配。 按照以下步骤列出用户的访问权限，以及列出不同范围的访问权限。

### <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

1. 在导航列表中选择“Azure Active Directory”。

1. 选择“用户”，以便打开“所有用户”。

   ![Azure Active Directory 的“所有用户”边栏选项卡](./media/role-assignments-portal/aad-all-users.png)

1. 在列表中选择单个用户。

1. 在“管理”部分，选择“Azure 资源”。

   ![Azure Active Directory 用户 Azure 资源](./media/role-assignments-portal/aad-user-azure-resources.png)

   在“Azure 资源”边栏选项卡上，可以看到所选用户和所选订阅的角色分配。 此列表仅包括你有权读取的资源的角色分配。 例如，如果用户还有你无法读取的角色分配，则这些角色分配将不会显示在列表中。

1. 如果有多个订阅，则可以选择“订阅”下拉列表，以查看不同订阅中的角色分配。

### <a name="list-role-assignments-for-a-resource-group"></a>列出资源组的角色分配

1. 在导航列表中，选择“资源组”。

1. 选择资源组，然后选择“访问控制(IAM)”。

   在“访问控制(IAM)”（也称标识和访问管理）边栏选项卡上，可以查看谁可以访问此资源组。 请注意，有些角色的权限范围已划归到**此资源**，还有一些角色是从另一范围 **(继承的)**。 特定于资源组分配访问权限，或者从父订阅的分配继承访问权限。

   ![资源组](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>列出订阅的角色分配

1. 在 Azure 门户中选择“所有服务”，然后选择“订阅”。

1. 选择订阅。

1. 选择“访问控制(IAM)”。

    在“访问控制(IAM)”边栏选项卡上，可以查看谁有此订阅及其角色的访问权限。

    ![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/subscription-access-control.png)

    经典订阅管理员和协同管理员被视为 RBAC 模型中订阅的所有者。

### <a name="list-role-assignments-for-a-management-group"></a>列出管理组的角色分配

1. 在 Azure 门户中选择“所有服务”，然后选择“管理组”。

1. 选择管理组。

1. 选择所选管理组对应的“(详细信息)”。

    ![管理组](./media/role-assignments-portal/management-groups-list.png)

1. 选择“访问控制(IAM)”。

    在“访问控制(IAM)”边栏选项卡上，可以查看谁有此管理组及其角色的访问权限。

    ![管理组的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>授予访问权限

在 RBAC 中，若要授予访问权限，请创建角色分配。 通过以下步骤在不同的范围授予访问权限。

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>创建资源组范围的角色分配

1. 在导航列表中，选择“资源组”。

1. 选择资源组。

1. 选择“访问控制(IAM)”，在资源组范围查看角色分配的当前列表。

   ![资源组的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. 选择“添加”，打开“添加权限”窗格。

   如果无权分配角色，则不会看到“添加”选项。

   ![“添加权限”窗格](./media/role-assignments-portal/add-permissions.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。

1. 在“选择”列表中，选择用户、组或应用程序。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 选择“保存”，创建角色分配。

   片刻之后，会在资源组范围为安全主体分配角色。

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>创建订阅范围的角色分配

1. 在 Azure 门户中选择“所有服务”，然后选择“订阅”。

1. 选择订阅。

1. 选择“访问控制(IAM)”，在订阅范围查看角色分配的当前列表。

   ![订阅的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/grant-subscription-access-control.png)

1. 选择“添加”，打开“添加权限”窗格。

   如果无权分配角色，则不会看到“添加”选项。

   ![“添加权限”窗格](./media/role-assignments-portal/add-permissions.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。

1. 在“选择”列表中，选择用户、组或应用程序。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 选择“保存”，创建角色分配。

   片刻之后，会在订阅范围为安全主体分配角色。

### <a name="create-a-role-assignment-at-a-management-group-scope"></a>创建管理组范围的角色分配

1. 在 Azure 门户中选择“所有服务”，然后选择“管理组”。

1. 选择管理组。

1. 选择所选管理组对应的“(详细信息)”。

    ![管理组](./media/role-assignments-portal/management-groups-list.png)

1. 选择“访问控制(IAM)”，在订阅范围查看角色分配的当前列表。

   ![管理组的“访问控制(IAM)”边栏选项卡](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. 选择“添加”，打开“添加权限”窗格。

   如果无权分配角色，则不会看到“添加”选项。

   ![“添加权限”窗格](./media/role-assignments-portal/add-permissions-management-groups.png)

1. 在“角色”下拉列表中选择一个角色，例如“管理组参与者”。

    若要了解可以对不同角色的管理组执行哪些受支持的操作，请参阅[使用 Azure 管理组来组织资源](../azure-resource-manager/management-groups-overview.md#management-group-access)。

1. 在“选择”列表中，选择用户、组或应用程序。 如果没有在列表中看到安全主体，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

1. 选择“保存”，创建角色分配。

   片刻之后，会在管理组范围为安全主体分配角色。

## <a name="remove-access"></a>删除访问权限

在 RBAC 中，若要删除访问权限，请删除角色分配。 按这些步骤删除访问权限。

### <a name="remove-a-role-assignment"></a>删除角色分配

1. 针对需要删除其角色分配的管理组、订阅、资源组或资源，打开“访问控制(IAM)”边栏选项卡。

1. 在角色分配列表中，在需删除其角色分配的安全主体旁边添加复选标记。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-select.png)

1. 选择“删除”。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，选择“是”。

    不能删除继承的角色分配。 如果需要删除继承的角色分配，则必须在创建角色分配的作用域上进行操作。 在“作用域”列的“(继承)”旁，有一条链接指向分配了此角色的范围。 请转到该处列出的范围以删除角色分配。

   ![“删除角色分配”消息](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 RBAC 和 Azure 门户授予用户的访问权限](quickstart-assign-role-user-portal.md)
* [教程：使用 RBAC 和 Azure PowerShell 授予用户的访问权限](tutorial-role-assignments-user-powershell.md)
* [内置角色](built-in-roles.md)
* [使用 Azure 管理组来组织资源](../azure-resource-manager/management-groups-overview.md)
