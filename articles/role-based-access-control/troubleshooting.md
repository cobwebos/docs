---
title: 对 Azure 资源的 RBAC 问题进行故障排除 | Microsoft Docs
description: 对 Azure 资源基于角色的访问控制 (RBAC) 问题进行故障排除。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 2351e6a63723156cce646a6a1cdda837b18a8f91
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456833"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>对 Azure 资源的 RBAC 问题进行故障排除

本文解答有关 Azure 资源的基于角色的访问控制 (RBAC) 的常见问题，以便你了解在 Azure 门户中使用角色时可能出现的情况，并可对访问问题进行故障排除。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色分配出现问题

- If you are unable to add a role assignment in the Azure portal on **Access control (IAM)** because the **Add** > **Add role assignment** option is disabled or because you get the permissions error "The client with object id does not have authorization to perform action", check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleAssignments/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator) at the scope you are trying to assign the role.
- If you get the error message "No more role assignments can be created (code: RoleAssignmentLimitExceeded)" when you try to assign a role, try to reduce the number of role assignments by assigning roles to groups instead. Azure 对于每个订阅最多支持 **2000** 个角色分配。 This role assignments limit is fixed and cannot be increased.

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- If you need steps for how to create a custom role, see the custom role tutorials using [Azure PowerShell](tutorial-custom-role-powershell.md) or [Azure CLI](tutorial-custom-role-cli.md).
- If you are unable to update an existing custom role, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleDefinition/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator).
- If you are unable to delete a custom role and get the error message "There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)", then there are role assignments still using the custom role. 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 No more role definitions can be created (code: RoleDefinitionLimitExceeded)" when you try to create a new custom role, delete any custom roles that aren't being used. Azure supports up to **5000** custom roles in a tenant. （Azure 政府、Azure 德国、Azure 中国世纪互联等专用云的限制为 2000 个自定义角色。）
- If you get an error similar to "The client has permission to perform action 'Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}', however the linked subscription was not found" when you try to update a custom role, check whether one or more [assignable scopes](role-definitions.md#assignablescopes) have been deleted in the tenant. 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>在租户之间移动订阅时恢复 RBAC

- If you need steps for how to transfer a subscription to a different Azure AD tenant, see [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md).
- 如果将订阅转让给其他 Azure AD 租户，所有角色分配都将从源 Azure AD 租户中永久删除，而不会迁移到目标 Azure AD 租户。 必须在目标租户中重新创建角色分配。 You also have to manually recreate managed identities for Azure resources. For more information, see [FAQs and known issues with managed identities](../active-directory/managed-identities-azure-resources/known-issues.md).
- If you are an Azure AD Global Administrator and you don't have access to a subscription after it was moved between tenants, use the **Access management for Azure resources** toggle to temporarily [elevate your access](elevate-access-global-admin.md) to get access to the subscription.

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- If you are having issues with Service administrator or Co-administrators, see [Add or change Azure subscription administrators](../billing/billing-add-change-azure-subscription-administrator.md) and [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Access denied or permission errors

- If you get the permissions error "The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)" when you try to create a resource, check that you are currently signed in with a user that is assigned a role that has write permission to the resource at the selected scope. 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅 [Azure 资源的内置角色](built-in-roles.md)。
- If you get the permissions error "You don't have permission to create a support request" when you try to create or update a support ticket, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Support/supportTickets/write` permission, such as [Support Request Contributor](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Role assignments with Unknown security principal

If you assign a role to a security principal (user, group, service principal, or managed identity) and then you later delete that security principal without removing the role assignment, the security principal type for the role assignment will be listed as **Unknown**. The following screenshot shows an example in the Azure portal. The security principal name is listed as **Identity deleted** and **Identity no longer exists**. 

![Web 应用程序资源组](./media/troubleshooting/unknown-security-principal.png)

If you list this role assignment using Azure PowerShell, you will see an empty `DisplayName` and an `ObjectType` set to Unknown. For example, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returns a role assignment that is similar to the following:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Similarly, if you list this role assignment using Azure CLI, you will see an empty `principalName`. For example, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) returns a role assignment that is similar to the following:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

It isn't a problem to leave these role assignments, but you can remove them using steps that are similar to other role assignments. For information about how to remove role assignments, see [Azure portal](role-assignments-portal.md#remove-role-assignments), [Azure PowerShell](role-assignments-powershell.md#remove-access), or [Azure CLI](role-assignments-cli.md#remove-access)

In PowerShell, if you try to remove the role assignments using the object ID and role definition name, and more than one role assignment matches your parameters, you will get the error message: "The provided information does not map to a role assignment". The following shows an example of the error message:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

If you get this error message, make sure you also specify the `-Scope` or `-ResourceGroupName` parameters.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>未检测到 RBAC 更改

Azure 资源管理器有时会缓存配置和数据以提高性能。 创建或删除角色分配时，更改最多可能需要 30 分钟才能生效。 如果使用的是 Azure 门户、Azure PowerShell 或 Azure CLI，则可以通过注销和登录来强制刷新角色分配更改。 如果使用 REST API 调用进行角色分配更改，则可以通过刷新访问令牌来强制刷新。

## <a name="web-app-features-that-require-write-access"></a>需要写访问权限的 Web 应用功能

如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 Web 应用具有**写**访问权限（参与者或所有者），并且在任何只读方案中不可用。

* 命令（例如启动、停止等。）
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 访问发布凭据和其他机密（如应用设置和连接字符串）
* 流式处理日志
* 诊断日志配置
* 控制台（命令提示符）
* 活动和最新部署（适用于本地 Git 连续部署）
* 估计费用
* Web 测试
* 虚拟网络（如果虚拟网络是以前具有写访问权限的用户所配置的，则仅对读者可见）。

如果无法访问以上任何磁贴，则需要让管理员提供对 Web 应用的“参与者”访问权限。

## <a name="web-app-resources-that-require-write-access"></a>需要写访问权限的 Web 应用资源

由于存在几个相互作用的不同资源，Web 应用程序是复杂的。 下面是包含几个网站的典型资源组：

![Web 应用程序资源组](./media/troubleshooting/website-resource-model.png)

因此，如果只授予某人对 Web 应用的访问权限，则 Azure 门户中的网站边栏选项卡上的很多功能将被禁用。

这些项需要对与网站对应的**应用服务计划**具有**写**访问权限：  

* 查看 Web 应用的定价层（“免费”或“标准”）  
* 规模配置（实例数、虚拟机大小、自动缩放设置）  
* 配额（存储、带宽、CPU）  

这些项需要对包含网站的整个**资源组**具有**写**访问权限：  

* SSL 证书和绑定（SSL 证书可以在同一资源组和地理位置中的站点之间共享）  
* 预警规则  
* 自动缩放设置  
* Application Insights 组件  
* Web 测试  

## <a name="virtual-machine-features-that-require-write-access"></a>需要写访问权限的虚拟机功能

与 Web 应用类似，虚拟机边栏选项卡上的某些功能需要对虚拟机或资源组中的其他资源具有写访问权限。

虚拟机与域名、虚拟网络、存储帐户和警报规则相关。

这些项需要对**虚拟机**具有**写**访问权限：

* 终结点  
* IP 地址  
* 磁盘  
* 扩展  

这些项需要对**虚拟机**和其所在的**资源组**（以及域名）具有**写**访问权限：  

* 可用性集  
* 负载均衡集  
* 预警规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和写访问权限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 For example, if a user is assigned the [Reader](built-in-roles.md#reader) role, they will not be able to view the functions within a function app. 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置。 To access these features, you will need the [Contributor](built-in-roles.md#contributor) role.

## <a name="next-steps"></a>后续步骤

- [Troubleshoot for guest users](role-assignments-external-users.md#troubleshoot)
- [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](role-assignments-portal.md)
- [查看 Azure 资源的 RBAC 更改的活动日志](change-history-report.md)

