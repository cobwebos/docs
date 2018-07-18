---
title: 为 Azure Active Directory 中的全局管理员提升访问权限 | Microsoft Docs
description: 介绍如何使用 Azure 门户或 REST API 为 Azure Active Directory 中的全局管理员提升访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0abf0a5971435fc3842a93e79d39468cba5c74da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445205"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>为 Azure Active Directory 中的全局管理员提升访问权限

如果你是 Azure Active Directory (Azure AD) 中的[全局管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator)，则有时可能需要执行以下操作：

- 在用户失去访问权限时重新获取对 Azure 订阅的访问权限
- 授予其他用户或自己对 Azure 订阅的访问权限
- 查看组织中的所有 Azure 订阅
- 允许自动化应用（例如发票或审计应用）访问所有 Azure 订阅

默认情况下，Azure AD 管理员角色和 Azure 基于角色的访问控制 (RBAC) 角色不会跨越 Azure AD 和 Azure。 但是，如果你是 Azure AD 中的全局管理员，则可提升访问权限以管理 Azure 订阅和管理组。 如果提升访问权限，你将被授予特定租户的所有订阅中的[用户访问管理员](built-in-roles.md#user-access-administrator)角色（RBAC 角色）。 借助用户访问管理员角色，你可授予其他用户对根范围 (`/`) 内的 Azure 资源的访问权限。

此提升应该是临时的，并且只能在需要时执行。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>使用 Azure 门户为全局管理员提升访问权限

1. 登录 [Azure 门户](https://portal.azure.com)或 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

1. 在导航列表中，单击“Azure Active Directory”，然后单击“属性”。

   ![Azure AD 属性 - 屏幕截图](./media/elevate-access-global-admin/aad-properties.png)

1. 在“全局管理员可管理 Azure 订阅和管理组”下，将开关设置为“是”。

   ![全局管理员可管理 Azure 订阅和管理组 - 屏幕截图](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   如果将开关设置为“是”，全局管理员帐户（当前登录用户）会在根范围 (`/`) 内添加到 Azure RBAC 的用户访问管理员角色，此角色可授予你查看和报告与 Azure AD 租户关联的所有 Azure 订阅的访问权限。

   如果将开关设置为“否”，全局管理员帐户（当前登录用户）会从 Azure RBAC 的用户访问管理员角色中删除。 你无法看到与 Azure AD 租户关联的所有 Azure 订阅，并且只能查看和管理已被授予访问权限的 Azure 订阅。

1. 单击“保存”，保存设置。

   此设置不是全局属性，仅适用于当前登录用户。

1. 在提升访问权限下执行需要完成的任务。 完成后，将开关重新设置为“否”。

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>使用 PowerShell 列出根范围 (/) 内的角色分配

若要列出用户在根范围 (`/`) 内的用户访问管理员角色分配，请使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 命令。

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>使用 PowerShell 在根范围 (/) 删除角色分配

若要在根范围 (`/`) 删除用户的用户访问管理员角色分配，请运行 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) 命令。

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>使用 REST API 为全局管理员提升访问权限

使用以下基本步骤，通过 REST API 为全局管理员提升访问权限。

1. 使用 REST 调用 `elevateAccess`，这将授予你根范围 (`/`) 内的用户访问管理员角色。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 创建[角色分配](/rest/api/authorization/roleassignments)，以便在任意范围分配任意角色。 以下示例显示用于在根范围 (`/`) 内分配 {roleDefinitionID} 角色的属性：

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. 作为用户访问管理员，还可以在根范围 (`/`) 删除角色分配。

1. 撤销用户访问管理员特权一直到再次需要时。

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>使用 REST API 在根范围 (/) 列出角色分配

可以在根范围 (`/`) 列出用户的所有角色分配。

- 调用 [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope)，其中 `{objectIdOfUser}` 是要检索其角色分配的用户的对象 ID。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>使用 REST API 撤销提升的访问权限

调用 `elevateAccess` 即为自己创建角色分配，因此若要撤销这些特权，需要删除分配。

1. 调用 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)，其中 `roleName` = 用户访问管理员，由此确定用户访问管理员角色的名称 ID。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    保存 `name` 参数中的 ID，在本例中为 `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`。

2. 你还需要列出租户管理员在租户范围内的角色分配。 对于执行了提升访问权限调用的租户管理员，列出其 `principalId` 在租户范围内的所有分配。 这将为 objectid 列出租户中的所有分配。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >租户管理员不应拥有多个分配，如果前面的查询返回过多分配，你也可以只在租户范围级别查询所有分配，然后筛选结果：`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. 上述调用将返回角色分配列表。 在范围 `"/"` 查找以下角色分配：`roleDefinitionId` 以第 1 步中的角色名称 ID 结尾，并且 `principalId` 与租户管理员的 objectId 一致。 
    
    示例角色分配：

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    同样，保存 `name` 参数的 ID，在本例中为 e7dd75bc-06f6-4e71-9014-ee96a929d099。

    3. 最后，使用角色分配 ID 删除 `elevateAccess` 添加的分配：

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>后续步骤

- [使用 REST 进行基于角色的访问控制](role-assignments-rest.md)
- [使用 Privileged Identity Management 管理对 Azure 资源的访问权限](pim-azure-resource.md)
- [使用条件访问管理对 Azure 管理的访问权限](conditional-access-azure-management.md)
