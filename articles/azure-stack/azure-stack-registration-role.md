---
title: 如何为 Azure Stack 中创建注册角色
description: 如何创建自定义的角色，以避免使用全局管理员身份进行注册。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.openlocfilehash: 89fe0889e08da2365523b27262e912ff5403f7f1
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233160"
---
# <a name="create-a-registration-role-for-azure-stack"></a>为 Azure Stack 中创建注册角色

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

对于您不想要在 Azure 订阅中提供所有者权限的情况下，可以创建自定义角色将权限分配给要注册 Azure Stack 的用户帐户。

> [!WARNING]
> 这不是安全局势功能。 它在方案中使用放置约束，以防止意外更改对 Azure 订阅。 如果某个用户是对此自定义角色的委派的权限，用户有权编辑的权限和提升权限。 仅将用户信任该自定义角色分配。

注册 Azure Stack 时, 注册帐户将需要以下 Azure Active Directory 权限和 Azure 订阅权限：

* **在 Azure Active Directory 租户中的应用程序注册权限：** 管理员具有应用程序注册权限。 用户的权限是租户中的所有用户的全局设置。 若要查看或更改设置，请参阅[创建 Azure AD 应用程序和服务主体可访问资源的](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

    *用户可以注册应用程序*设置必须设置为**是**，以便启用用户帐户来注册 Azure Stack。 如果应用注册设置设置为**否**，不能使用用户帐户，必须使用全局管理员帐户注册 Azure Stack。

* **足够的 Azure 订阅权限的一组：** 所有者组中的用户具有足够的权限。 对于其他帐户，可以分配的权限集通过将自定义角色分配以下各节中所述。

## <a name="create-a-custom-role-using-powershell"></a>创建使用 PowerShell 的自定义角色

若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](../role-based-access-control/built-in-roles.md#owner)或[用户访问权限管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)。 使用以下 JSON 模板来简化定义自定义角色。 该模板将创建一个自定义角色，允许所需的读取和写入访问权限的 Azure Stack 注册。

1. 创建 JSON 文件。 例如 `C:\CustomRoles\registrationrole.json`
2. 将以下 JSON 添加到该文件。 将 <SubscriptionID> 替换为你的 Azure 订阅 ID。

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，连接到 Azure 以使用 Azure 资源管理器。 出现提示时，身份验证使用具有足够的权限的帐户，如[所有者](../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)。

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 若要将角色添加到订阅，请使用**New-azurermroledefinition**指定 JSON 模板文件。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>注册角色向用户分配

创建注册自定义角色后，将分配角色用户注册 Azure Stack。

1. 使用 Azure 订阅上具有足够的权限的帐户登录，以委派权限-例如[所有者](../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)。
2. 在中**订阅**，选择**访问控制 (IAM) > 添加角色分配**。
3. 在中**角色**，选择你创建的自定义角色*Azure Stack 注册角色*。
4. 选择你想要分配到角色的用户。
5. 选择**保存**要将所选的用户分配到角色。

    ![选择要将分配给角色的用户](media/azure-stack-registration-role/assign-role.png)

使用自定义角色的详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤

[将 Azure Stack 注册到 Azure](azure-stack-registration.md)
