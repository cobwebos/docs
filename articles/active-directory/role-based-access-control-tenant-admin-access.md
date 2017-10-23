---
title: "租户管理员提升访问权限 - Azure AD | Microsoft Docs"
description: "本主题介绍适用于基于角色的访问控制 (RBAC) 的内置角色。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.openlocfilehash: 22b62be1773c5042ecf6ee078e68a4ffdf791d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>通过基于角色的访问控制，作为租户管理员提升访问权限

基于角色的访问控制帮助租户管理员获取临时的访问权限提升，以便其可以授予高于普通的权限。 需要时，租户管理员可将自己提升为用户访问管理员角色。 该角色让租户管理员有权在“/”范围内向自己或其他人授予角色。

此功能非常重要，因为它允许租户管理员查看组织中存在的所有订阅。 它还允许自动化应用（如开票和审核）访问所有订阅，并针对计费或资产管理提供有关组织状态的准确观点。  

## <a name="how-to-use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>如何通过 Azure AD 管理中心使用 elevateAccess 进行租户访问

在 [Azure Active Directory 管理中心](https://aad.portal.azure.com)内，可以通过**属性**调用此功能。
此功能称为“全局管理员可以管理 Azure 订阅”。 印象中这是 Azure Active Directory 的全局属性，但是，它对于当前已登录用户基于每个用户工作。 如果在 Azure Active Directory 中具有全局管理员权限，可以对当前已登录到 Azure Active Directory 管理中心的用户调用 elevateAccess 功能。

依次选择“是”、“保存”：这将为当前已登录到门户的用户**分配**根目录“/”（根范围）下的“用户访问管理员”角色。

依次选择“否”、“保存”：这将为当前已登录到门户的用户**删除**根目录“/”（根范围）下的“用户访问管理员”角色。

![Azure AD 管理中心 - 属性 - Globaladmin 可以管理 Azure 订阅 - 屏幕截图](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="how-to-use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>如何通过 REST API 使用 elevateAccess 提供租户访问权限

基本过程包含以下步骤：

1. 使用 REST 调用 *elevateAccess*，这将授予“/”范围的用户访问管理员角色。

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. 创建[角色分配](/rest/api/authorization/roleassignments)，以便在任意范围分配任意角色。 以下示例显示用于在“/”范围分配读取者角色的属性：

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
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

1.  调用 [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get)，其中 roleName = 用户访问管理员，由此确定用户访问管理员角色的名称 GUID。 响应应为如下所示：

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    保存名称参数的 GUID，在本例中为 **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**。

2. 调用 [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get)，其中 principalId = 自己的 ObjectId。 这会列出租户中的所有分配。 查找范围为“/”、RoleDefinitionId 以步骤 1 中找到的角色名称 GUID 结尾的分配。 角色分配应为如下所示：

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    同样，保存名称参数的 GUID，在本例中为 **e7dd75bc-06f6-4e71-9014-ee96a929d099**。

3. 最后，调用 [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById)，其中 roleAssignmentId = 步骤 2 中找到的名称 GUID。

## <a name="next-steps"></a>后续步骤

- 了解有关[使用 REST 管理基于角色的访问控制](role-based-access-control-manage-access-rest.md)的详细信息

- 在 Azure 门户中[管理访问权限分配](role-based-access-control-manage-assignments.md)
