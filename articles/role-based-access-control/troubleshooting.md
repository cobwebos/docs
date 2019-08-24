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
ms.openlocfilehash: c3ca873cb4dd49d77ba818b3d05c3aa41e89276f
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982723"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>对 Azure 资源的 RBAC 问题进行故障排除

本文解答有关 Azure 资源的基于角色的访问控制 (RBAC) 的常见问题，以便你了解在 Azure 门户中使用角色时可能出现的情况，并可对访问问题进行故障排除。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色分配出现问题

- 如果你因为“添加” > “添加角色分配”选项被禁用或者因为收到权限错误“具有此对象 id 的客户端无权执行操作”而无法在 Azure 门户中的“访问控制(IAM)”上添加角色分配，请检查你当前登录时使用的用户是否为在你尝试分配角色的范围中具有 `Microsoft.Authorization/roleAssignments/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果尝试分配角色时收到错误消息“无法创建更多的角色分配(代码:RoleAssignmentLimitExceeded)”，请尝试通过改为将角色分配给组来减少角色分配数。 Azure 对于每个订阅最多支持 **2000** 个角色分配。

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如果需要了解有关如何创建自定义角色的步骤，请参阅使用 [Azure PowerShell](tutorial-custom-role-powershell.md) 或 [Azure CLI](tutorial-custom-role-cli.md) 自定义角色的教程。
- 如果你无法更新现有的自定义角色，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Authorization/roleDefinition/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果你无法删除自定义角色并且收到错误消息“已存在引用此角色的角色分配(代码:RoleDefinitionHasAssignments)”，则表明存在仍然使用此自定义角色的角色分配。 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 无法创建更多的角色定义(代码:RoleDefinitionLimitExceeded)”，请删除未在使用的任何自定义角色。 Azure 在一个租户中最多支持 **5000** 个自定义角色。 （Azure 政府、Azure 德国、Azure 中国世纪互联等专用云的限制为 2000 个自定义角色。）
- 如果尝试更新自定义角色时收到类似于“客户端具有在范围 '/subscriptions/{subscriptionid}' 上执行操作 'Microsoft.Authorization/roleDefinitions/write' 的权限，但是未找到链接的订阅”的错误，请检查是否已在租户中删除了一个或多个[可分配的范围](role-definitions.md#assignablescopes)。 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>在租户之间移动订阅时恢复 RBAC

- 如果你需要了解将订阅转让给其他 Azure AD 租户的步骤，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)。
- 如果将订阅转让给其他 Azure AD 租户，所有角色分配都将从源 Azure AD 租户中永久删除，而不会迁移到目标 Azure AD 租户。 必须在目标租户中重新创建角色分配。 此外，还需手动重新创建 Azure 资源的托管标识。 有关详细信息，请参阅[托管标识的 FAQ 和已知问题](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果你是 Azure AD 全局管理员并且在租户之间移动某个订阅后对其没有访问权限，请使用“Azure 资源的访问权限管理”开关暂时[提升你的访问权限](elevate-access-global-admin.md)来获取对订阅的访问权限。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果遇到服务管理员或协同管理员方面的问题，请参阅[添加或更改 Azure 订阅管理员](../billing/billing-add-change-azure-subscription-administrator.md)以及[经典订阅管理员角色、Azure RBAC 角色和 Azure AD管理员角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>访问被拒绝或权限错误

- 如果尝试创建资源时收到权限错误“具有此对象 id 的客户端无权在此作用域内执行操作(代码:AuthorizationFailed)”，请检查你当前登录时使用的用户是否分配有在所选作用域内对资源具有写入权限的角色。 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅 [Azure 资源的内置角色](built-in-roles.md)。
- 如果尝试创建或更新支持票证时收到权限错误“无权创建支持票证”，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Support/supportTickets/write` 权限的角色，例如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-without-a-security-principal"></a>没有安全主体的角色分配

使用 Azure PowerShell 列出角色分配时，可能会看到分配的 `DisplayName` 为空且 `ObjectType` 设置为“未知”。 例如，[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 返回的角色分配如下所示：

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

类似地，使用 Azure CLI 列出角色分配时，可能会看到分配的 `principalName` 为空。 例如，[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) 返回的角色分配如下所示：

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

如果将角色分配给安全主体（用户、组、服务主体或托管标识），然后又删除该安全主体，则会进行此类角色分配。 这些角色分配不显示在 Azure 门户中，留下它们没有问题。 不过，你可以根据需要删除这些角色分配。

若要删除这些角色分配，请使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 或 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) 命令。

在 PowerShell 中，如果尝试通过对象 ID 和角色定义名称来删除角色分配，而多个角色分配与参数相匹配，则会出现错误消息：“提供的信息未映射到角色分配”。 下面显示了错误消息示例：

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果出现此错误消息，请确保还指定了 `-Scope` 或 `-ResourceGroupName` 参数。

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
* 警报规则  
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
* Extensions  

这些项需要对**虚拟机**和其所在的**资源组**（以及域名）具有**写**访问权限：  

* 可用性集  
* 负载均衡集  
* 警报规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和写访问权限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如, 如果为用户分配了 "[读者](built-in-roles.md#reader)" 角色, 则他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置。 若要访问这些功能, 您将需要 "[参与者](built-in-roles.md#contributor)" 角色。

## <a name="next-steps"></a>后续步骤
* [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](role-assignments-portal.md)
* [查看 Azure 资源的 RBAC 更改的活动日志](change-history-report.md)

