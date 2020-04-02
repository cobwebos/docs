---
title: 排除 Azure RBAC 故障
description: 使用 Azure 基于角色的访问控制 （Azure RBAC） 解决问题。
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 09d5b7a126a1b8832bfe40e2e25dd4000d5d9155
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548288"
---
# <a name="troubleshoot-azure-rbac"></a>排除 Azure RBAC 故障

本文回答了有关基于 Azure 角色的访问控制 （Azure RBAC） 的一些常见问题，以便了解使用角色时会发生什么，并可以排除访问问题。

## <a name="azure-role-assignments-limit"></a>Azure 角色分配限制

Azure 支持每个订阅最多**2000**个角色分配。 如果在尝试分配角色时收到错误消息"无法创建更多角色分配（代码：角色分配限制超过）"，请尝试减少订阅中的角色分配数。

> [!NOTE]
> 每个订阅的**2000**角色分配限制已固定，无法增加。

如果您接近此限制，以下是减少角色分配数的一些方法：

- 将用户添加到组，并将角色分配给组。 
- 将多个内置角色与自定义角色合并。 
- 在较高范围内（如订阅或管理组）进行常见角色分配。
- 如果您有 Azure AD 高级 P2，则使角色分配符合[Azure AD 特权标识管理](../active-directory/privileged-identity-management/pim-configure.md)的条件，而不是永久分配。 
- 添加其他订阅。 

要获取角色分配数，可以在 Azure 门户[中的"访问控制 （IAM）"页上查看图表](role-assignments-list-portal.md#list-number-of-role-assignments)。 您还可以使用以下 Azure PowerShell 命令：

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure 角色分配问题

- 如果你因为****“添加” > “添加角色分配”**** 选项被禁用或者因为收到权限错误“具有此对象 id 的客户端无权执行操作”而无法在 Azure 门户中的“访问控制(IAM)”**** 上添加角色分配，请检查你当前登录时使用的用户是否为在你尝试分配角色的范围中具有 `Microsoft.Authorization/roleAssignments/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如果需要如何创建自定义角色的步骤，请参阅使用[Azure 门户](custom-roles-portal.md)（当前预览版[）、Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)的自定义角色教程。
- 如果你无法更新现有的自定义角色，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Authorization/roleDefinition/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果无法删除自定义角色并获取错误消息"存在引用角色的现有角色分配（代码：角色定义任务）"，则仍有角色分配使用自定义角色。 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 当您尝试创建新的自定义角色时，无法创建更多角色定义（代码：角色定义限制超过）"，删除未使用的任何自定义角色。 Azure 支持目录中最多**5000**个自定义角色。 （对于 Azure 德国和 Azure 中国 21Vianet，限制为 2000 个自定义角色。
- 如果在尝试更新自定义角色时收到类似于"客户端有权对作用域"/订阅/订阅/订阅 id"执行操作"Microsoft.授权/角色定义/写入"类似的错误，请检查目录中是否删除了一个或多个[可分配作用域](role-definitions.md#assignablescopes)。 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="custom-roles-and-management-groups"></a>自定义角色和管理组

- 只能在自定义角色中`AssignableScopes`定义一个管理组。 将管理组添加到`AssignableScopes`当前处于预览状态。
- 无法在管理`DataActions`组作用域中分配 具有的自定义角色。
- Azure 资源管理器不验证管理组在角色定义的可分配作用域中是否存在。
- 有关自定义角色和管理组的详细信息，请参阅[使用 Azure 管理组组织资源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。

## <a name="transferring-a-subscription-to-a-different-directory"></a>将订阅传输到其他目录

- 如果需要如何将订阅转移到其他 Azure AD 目录的步骤，请参阅[将 Azure 订阅的所有权转移到其他帐户](../cost-management-billing/manage/billing-subscription-transfer.md)。
- 如果将订阅转移到其他 Azure AD 目录，则所有角色分配都将从源 Azure AD 目录**永久**删除，并且不会迁移到目标 Azure AD 目录。 您必须在目标目录中重新创建角色分配。 此外，还需手动重新创建 Azure 资源的托管标识。 有关详细信息，请参阅[托管标识的 FAQ 和已知问题](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果您是 Azure AD 全局管理员，并且在订阅在目录之间传输后无法访问该订阅，请使用**Azure 资源的"访问"管理**切换以临时[提升访问权限](elevate-access-global-admin.md)以访问订阅。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果服务管理员或共同管理员遇到问题，请参阅[添加或更改 Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)和[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>访问被拒绝或权限错误

- 如果在尝试创建资源时收到权限错误"具有对象 ID 的客户端无权执行范围操作（代码：授权失败）"，请检查您当前是否当前与已分配了对所选作用域中的资源具有写入权限的角色的用户登录。 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅 [Azure 资源的内置角色](built-in-roles.md)。
- 如果尝试创建或更新支持票证时收到权限错误“无权创建支持票证”，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Support/supportTickets/write` 权限的角色，例如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-with-unknown-security-principal"></a>具有未知安全主体的角色分配

如果将角色分配给安全主体（用户、组、服务主体或托管标识），然后在未删除角色分配的情况下删除该安全主体，则该角色分配的安全主体类型将列为“未知”****。 以下屏幕截图显示了 Azure 门户中的示例。 安全主体名称列为“标识已删除”**** 和“标识不再存在”****。 

![Web 应用程序资源组](./media/troubleshooting/unknown-security-principal.png)

如果使用 Azure PowerShell 列出此角色分配，你将看到空的 `DisplayName` 和设置为“Unknown”的 `ObjectType`。 例如，[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 返回的角色分配如下所示：

```
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

同样，如果使用 Azure CLI 列出此角色分配，你将看到空的 `principalName`。 例如，[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) 返回的角色分配如下所示：

```
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

保留这些角色分配不是问题，但你可以使用与其他角色分配相似的步骤将其删除。 有关如何删除角色分配的信息，请参阅 [Azure 门户](role-assignments-portal.md#remove-a-role-assignment)、[Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment) 或 [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

在 PowerShell 中，如果您尝试使用对象 ID 和角色定义名称删除角色分配，并且多个角色分配与您的参数匹配，则会收到错误消息："提供的信息不映射到角色分配"。 下面显示了错误消息示例：

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果出现此错误消息，请确保还指定了 `-Scope` 或 `-ResourceGroupName` 参数。

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>未检测到角色分配更改

Azure 资源管理器有时会缓存配置和数据以提高性能。 添加或删除角色分配时，更改最多可能需要 30 分钟才能生效。 如果使用的是 Azure 门户、Azure PowerShell 或 Azure CLI，则可以通过注销和登录来强制刷新角色分配更改。 如果使用 REST API 调用进行角色分配更改，则可以通过刷新访问令牌来强制刷新。

如果要在管理组作用域中添加或删除角色分配，并且角色具有`DataActions`，则数据平面上的访问权限可能无法更新几个小时。 这仅适用于管理组作用域和数据平面。

## <a name="web-app-features-that-require-write-access"></a>需要写访问权限的 Web 应用功能

如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 Web 应用（参与者或所有者）的**写入**访问权限，并且在任何只读方案中都不可用。

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

* TLS/SSL 证书和绑定（TLS/SSL 证书可以在同一资源组中的站点和地理位置之间共享）  
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
* 扩展  

这些项需要对**虚拟机**和其所在的**资源组**（以及域名）具有**写**访问权限：  

* 可用性集  
* 负载均衡集  
* 警报规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和写访问权限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果用户分配了[Reader](built-in-roles.md#reader)角色，则他们将无法在函数应用中查看函数。 门户将显示 (无访问权限)****。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置********。 若要访问这些功能，需要[参与者](built-in-roles.md#contributor)角色。

## <a name="next-steps"></a>后续步骤

- [针对来宾用户的疑难解答](role-assignments-external-users.md#troubleshoot)
- [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](role-assignments-portal.md)
- [查看 Azure 资源的 RBAC 更改的活动日志](change-history-report.md)
