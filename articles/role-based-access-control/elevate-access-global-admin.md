---
title: 租户管理员提升访问权限 - Azure AD | Microsoft Docs
description: 本主题介绍适用于基于角色的访问控制 (RBAC) 的内置角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: 7b4625bff277851fb9002e54b26485b948981252
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>通过基于角色的访问控制，作为租户管理员提升访问权限

基于角色的访问控制帮助租户管理员获取临时的访问权限提升，以便其可以授予高于普通的权限。 需要时，租户管理员可将自己提升为用户访问管理员角色。 该角色让租户管理员有权在“/”范围内向自己或其他角色授予权限。

此功能非常重要，因为它允许租户管理员查看组织中存在的所有订阅。 它还允许自动化应用（如开票和审核）访问所有订阅，并针对计费或资产管理提供有关组织状态的准确观点。  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>通过 Azure AD 管理中心使用 elevateAccess 进行租户访问

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)并使用凭据登录。

2. 从 Azure AD 的左侧菜单中选择“属性面板”。

3. 找到“全局管理员可以管理 Azure 订阅”，并依次选择“是”和“保存”。
    > [!IMPORTANT] 
    > 如果选择“是”，则为当前已登录到门户的用户分配根目录“/”（根范围）下的“用户访问管理员”角色。 这将允许用户查看其他所有的 Azure 订阅。
    
    > [!NOTE] 
    > 如果选择“否”，则为当前已登录到门户的用户删除根目录“/”（根范围）下的“用户访问管理员”角色。

> [!TIP] 
> 印象中这是 Azure Active Directory 的全局属性，但是，它对于当前已登录用户基于每个用户工作。 如果在 Azure Active Directory 中具有全局管理员权限，可以对当前已登录到 Azure Active Directory 管理中心的用户调用 elevateAccess 功能。

![Azure AD 管理中心 - 属性 - 全局管理员可以管理 Azure 订阅 - 屏幕截图](./media/elevate-access-global-admin/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>使用 PowerShell 查看“/”范围内的角色分配
若要查看 **/** 范围内的**用户访问管理员**分配，请使用 `Get-AzureRmRoleAssignment` PowerShell cmdlet。
    
```powershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_.SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**示例输出**：
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>使用 Powershell 删除“/”范围内的角色分配：
可以使用以下 PowerShell cmdlet 删除分配：

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>通过 REST API 使用 elevateAccess 提供租户访问权限

基本过程包含以下步骤：

1. 使用 REST 调用 *elevateAccess*，这将授予“/”范围的用户访问管理员角色。

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. 创建[角色分配](/rest/api/authorization/roleassignments)，以便在任意范围分配任意角色。 以下示例显示用于在“/”范围分配读取者角色的属性：

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

3. 作为用户访问管理员，还可以删除“/”范围的角色分配。

4. 撤销用户访问管理员特权，直至需要再次使用时。


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>如何使用 REST API 撤消 elevateAccess 操作

调用 *elevateAccess* 时，会为自己创建角色分配，因此若要撤消这些特权，需删除该分配。

1.  调用 GET roleDefinitions，其中 roleName = 用户访问管理员，由此确定用户访问管理员角色的名称 GUID。
    ```
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

    保存名称参数的 GUID，在本例中为 **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**。

2. 还需要列出租户作用域中租户管理员的角色分配。 对于进行了提升访问调用的 TenantAdmin，列出其 PrincipalId 在租户作用域中的所有分配。 这将列出 ObjectID 的租户中所有的分配。

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >租户管理员不应拥有多个分配，如果前面的查询返回过多分配，你也可以只在租户作用域级别查询所有分配，然后筛选结果：`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. 上述调用将返回角色分配列表。 查找作用域为“/”的角色分配，其中 RoleDefinitionId 以在步骤 1 中找到的角色名称 GUID 结尾，并且 PrincipalId 与租户管理员的 ObjectId 相匹配。 
    
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
        
    同样，保存名称参数的 GUID，在本例中为 **e7dd75bc-06f6-4e71-9014-ee96a929d099**。

    3. 最后，使用突出显示的 RoleAssignment ID 来删除由提升访问添加的分配：

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关[使用 REST 管理基于角色的访问控制](role-assignments-rest.md)的详细信息

- 在 Azure 门户中[管理访问权限分配](role-assignments-users.md)
