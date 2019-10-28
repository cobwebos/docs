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
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: e8a5b8b5794687f9e3b1707fda4cbe381e277317
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819766"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>对 Azure 资源的 RBAC 问题进行故障排除

本文解答有关 Azure 资源的基于角色的访问控制 (RBAC) 的常见问题，以便你了解在 Azure 门户中使用角色时可能出现的情况，并可对访问问题进行故障排除。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色分配出现问题

- 如果无法在**访问控制（IAM）** 的 Azure 门户中添加角色分配，因为 "**添加** > **添加角色分配**" 选项已禁用，或者你获取了权限错误 "具有对象 id 的客户端没有授权执行操作 "，请检查你当前是否已使用分配有 `Microsoft.Authorization/roleAssignments/write` 权限的用户（例如，在你尝试分配角色的范围内拥有"[所有者](built-in-roles.md#owner)"或"[用户访问管理员](built-in-roles.md#user-access-administrator)"角色）登录。
- 如果你在尝试分配角色时收到错误消息 "无法创建更多的角色分配（code： RoleAssignmentLimitExceeded）"，请尝试通过将角色分配给组来减少角色分配的数量。 Azure 对于每个订阅最多支持 **2000** 个角色分配。 此角色分配限制是固定的，不能增加。

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如果需要有关如何创建自定义角色的步骤，请参阅使用[Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)的自定义角色教程。
- 如果无法更新现有的自定义角色，请检查你当前是否已使用分配有 `Microsoft.Authorization/roleDefinition/write` 权限的用户（如 "[所有者](built-in-roles.md#owner)" 或 "[用户访问管理员](built-in-roles.md#user-access-administrator)"）登录。
- 如果无法删除自定义角色并收到错误消息 "存在角色分配引用角色（code： RoleDefinitionHasAssignments）"，则仍在使用自定义角色的角色分配。 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 创建新的自定义角色时，无法创建更多的角色定义（code： RoleDefinitionLimitExceeded） "。请删除任何未使用的自定义角色。 Azure 在租户中最多支持**5000**个自定义角色。 （Azure 政府、Azure 德国、Azure 中国世纪互联等专用云的限制为 2000 个自定义角色。）
- 如果收到类似于 "客户端有权在范围"/subscriptions/{subscriptionid} "上执行操作" roleDefinitions/write "的错误，但在尝试更新自定义角色时找不到链接的订阅"，请检查是否已在租户中删除一个或多个可[分配的作用域](role-definitions.md#assignablescopes)。 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>在租户之间移动订阅时恢复 RBAC

- 如果需要有关如何将订阅转移到不同 Azure AD 租户的步骤，请参阅[将 Azure 订阅的所有权转让给其他帐户](../billing/billing-subscription-transfer.md)。
- 如果将订阅转让给其他 Azure AD 租户，所有角色分配都将从源 Azure AD 租户中永久删除，而不会迁移到目标 Azure AD 租户。 必须在目标租户中重新创建角色分配。 还必须为 Azure 资源手动重新创建托管标识。 有关详细信息，请参阅[托管标识的常见问题解答和已知问题](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果你是 Azure AD 全局管理员，并且在租户之间移动订阅后你无法访问它，请使用**Azure 资源的访问管理**切换来暂时[提升你的访问](elevate-access-global-admin.md)权限，以获取对订阅的访问权限。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果你遇到服务管理员或共同管理员的问题，请参阅[添加或更改 azure 订阅管理员](../billing/billing-add-change-azure-subscription-administrator.md)和[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>拒绝访问或权限错误

- 如果你在尝试创建资源时收到权限错误 "具有对象 id 的客户端无权执行操作（code： AuthorizationFailed）"，请检查你当前是否已使用分配有写入权限的用户登录对所选范围内的资源的权限。 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅 [Azure 资源的内置角色](built-in-roles.md)。
- 如果你在尝试创建或更新支持票证时获得权限错误 "你没有创建支持请求的权限"，请检查你当前是否已使用分配有 `Microsoft.Support/supportTickets/write` 权限的角色的用户登录，例如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-with-unknown-security-principal"></a>具有未知安全主体的角色分配

当你使用 Azure PowerShell 列出你的角色分配时，可能会看到具有空 `DisplayName` 的分配，并且 `ObjectType` 设置为 "未知"。 例如， [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)返回类似于以下内容的角色分配：

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

同样，使用 Azure CLI 列出角色分配时，可能会看到具有空 `principalName`的分配。 例如， [az role 赋值 list](/cli/azure/role/assignment#az-role-assignment-list)返回类似于以下内容的角色分配：

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

当你向安全主体（用户、组、服务主体或托管标识）分配角色时，会发生这些角色分配，稍后会删除该安全主体。 这些角色分配不会显示在 Azure 门户中，因此不会出现问题。 不过，如果愿意，可以删除这些角色分配。

若要删除这些角色分配，请使用[AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)或[az role 赋值 delete](/cli/azure/role/assignment#az-role-assignment-delete)命令。

在 PowerShell 中，如果尝试使用对象 ID 和角色定义名称删除角色分配，并且有多个角色分配与参数匹配，则将收到错误消息： "提供的信息未映射到角色分配"。 下面显示了错误消息的示例：

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果收到此错误消息，请确保同时指定 `-Scope` 或 `-ResourceGroupName` 参数。

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

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果为用户分配了 "[读者](built-in-roles.md#reader)" 角色，则他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置。 若要访问这些功能，您将需要 "[参与者](built-in-roles.md#contributor)" 角色。

## <a name="next-steps"></a>后续步骤

- [来宾用户故障排除](role-assignments-external-users.md#troubleshoot)
- [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](role-assignments-portal.md)
- [查看 Azure 资源的 RBAC 更改的活动日志](change-history-report.md)

