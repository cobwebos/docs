---
title: 排查 Azure RBAC 问题
description: 使用 Azure 基于角色的访问控制（Azure RBAC）解决问题。
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
ms.date: 05/01/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 58e7a46633b7bbdd6074fa7e511569ff9e2aebdf
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996599"
---
# <a name="troubleshoot-azure-rbac"></a>排查 Azure RBAC 问题

本文解答了有关 Azure 基于角色的访问控制（Azure RBAC）的一些常见问题，以便您知道使用这些角色时的预期情况，并解决访问问题。

## <a name="azure-role-assignments-limit"></a>Azure 角色分配限制

Azure 对于每个订阅最多支持 **2000** 个角色分配。 此限制包括订阅、资源组和资源范围内的角色分配。 如果你收到错误消息 "无法创建更多的角色分配（code： RoleAssignmentLimitExceeded）"，则尝试分配角色时，请尝试减少订阅中的角色分配数。

> [!NOTE]
> 每个订阅的**2000**角色分配限制是固定的，不能增加。

如果接近此限制，可以通过以下方式减少角色分配的数量：

- 将用户添加到组，并改为将角色分配给组。 
- 将多个内置角色与自定义角色合并在一起。 
- 在更高的作用域（如订阅或管理组）上进行常见角色分配。
- 如果有 Azure AD Premium P2，请在[Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)中提供符合条件的角色分配，而不是永久分配。 
- 添加其他订阅。 

若要获取角色分配数，可以在 Azure 门户[的 "访问控制（IAM）" 页上查看图表](role-assignments-list-portal.md#list-number-of-role-assignments)。 你还可以使用以下 Azure PowerShell 命令：

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure 角色分配的问题

- 如果你因为****“添加” > “添加角色分配”**** 选项被禁用或者因为收到权限错误“具有此对象 id 的客户端无权执行操作”而无法在 Azure 门户中的“访问控制(IAM)”**** 上添加角色分配，请检查你当前登录时使用的用户是否为在你尝试分配角色的范围中具有 `Microsoft.Authorization/roleAssignments/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如果需要有关如何创建自定义角色的步骤，请参阅使用[Azure 门户](custom-roles-portal.md)（当前为预览版）的自定义角色教程、 [Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)。
- 如果你无法更新现有的自定义角色，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Authorization/roleDefinition/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果你无法删除自定义角色并且收到错误消息“已存在引用此角色的角色分配(代码:RoleDefinitionHasAssignments)”，则表明存在仍然使用此自定义角色的角色分配。 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 创建新的自定义角色时，无法创建更多的角色定义（code： RoleDefinitionLimitExceeded） "。请删除任何未使用的自定义角色。 Azure 最多支持**5000**目录中的自定义角色。 （对于 Azure 德国和 Azure 中国世纪互联，此限制为2000个自定义角色。）
- 如果收到类似于 "客户端有权在范围"/subscriptions/{subscriptionid} "上执行操作" roleDefinitions/write "的错误，但在尝试更新自定义角色时找不到链接的订阅"，请检查是否已在目录中删除一个或多个可[分配的作用域](role-definitions.md#assignablescopes)。 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="custom-roles-and-management-groups"></a>自定义角色和管理组

- 只能在自定义角色的中`AssignableScopes`定义一个管理组。 将管理组添加到`AssignableScopes`目前处于预览阶段。
- 无法在管理`DataActions`组范围内分配具有的自定义角色。
- Azure 资源管理器不会验证管理组是否存在于角色定义的可分配范围中。
- 有关自定义角色和管理组的详细信息，请参阅[通过 Azure 管理组来组织资源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。

## <a name="transferring-a-subscription-to-a-different-directory"></a>将订阅转移到不同的目录

- 如果需要有关如何将订阅转移到不同 Azure AD 目录的步骤，请参阅[将 Azure 订阅所有权转让给其他帐户](../cost-management-billing/manage/billing-subscription-transfer.md)。
- 如果将订阅转让给其他 Azure AD 目录，则所有角色分配都将从源 Azure AD 目录中**永久**删除，并且不会迁移到目标 Azure AD 目录。 你必须在目标目录中重新创建角色分配。 此外，还需手动重新创建 Azure 资源的托管标识。 有关详细信息，请参阅[托管标识的 FAQ 和已知问题](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果你是 Azure AD 全局管理员，并且在目录之间传输订阅后无权访问该订阅，请使用**Azure 资源的访问管理**切换来暂时[提升你的访问](elevate-access-global-admin.md)权限，以获取对订阅的访问权限。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果你在服务管理员或协同管理员方面遇到问题，请参阅[添加或更改 azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)和[经典订阅管理员角色、Azure 角色和 Azure AD 角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>访问被拒绝或权限错误

- 如果尝试创建资源时收到权限错误“具有此对象 id 的客户端无权在此作用域内执行操作(代码:AuthorizationFailed)”，请检查你当前登录时使用的用户是否分配有在所选作用域内对资源具有写入权限的角色。 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅[Azure 内置角色](built-in-roles.md)。
- 如果尝试创建或更新支持票证时收到权限错误“无权创建支持票证”，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Support/supportTickets/write` 权限的角色，例如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-with-identity-not-found"></a>找不到标识为的角色分配

在 Azure 门户的角色分配列表中，你可能会注意到，安全主体（用户、组、服务主体或托管标识）列出为**找不到**且类型**未知**的标识。

![Web 应用程序资源组](./media/troubleshooting/unknown-security-principal.png)

出于以下两个原因，可能找不到该标识：

- 你最近在创建角色分配时邀请了用户
- 删除了具有角色分配的安全主体

如果你最近在创建角色分配时邀请了用户，则此安全主体可能仍处于跨区域的复制过程中。 如果是这样，请稍等片刻，并刷新角色分配列表。

但是，如果此安全主体不是最近邀请的用户，则可能是已删除的安全主体。 如果向安全主体分配角色，然后在不首先删除角色分配的情况下删除该安全主体，则会将安全主体列为 "**找不到标识**" 和 "**未知**类型"。

如果使用 Azure PowerShell 列出此角色分配，则可能会看到一个空`DisplayName`的， `ObjectType`并且设置为 "**未知**"。 例如， [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)返回类似于以下输出的角色分配：

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

同样，如果使用 Azure CLI 列出此角色分配，则可能会看到一个空`principalName`的。 例如， [az role 赋值 list](/cli/azure/role/assignment#az-role-assignment-list)返回类似于以下输出的角色分配：

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

在删除安全主体的情况下，不会保留这些角色分配的问题。 如果需要，可以使用类似于其他角色分配的步骤来删除这些角色分配。 有关如何删除角色分配的信息，请参阅 [Azure 门户](role-assignments-portal.md#remove-a-role-assignment)、[Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment) 或 [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

在 PowerShell 中，如果尝试使用对象 ID 和角色定义名称删除角色分配，并且有多个角色分配与参数匹配，则将收到错误消息： "提供的信息未映射到角色分配"。 以下输出显示了错误消息的示例：

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

Azure 资源管理器有时会缓存配置和数据以提高性能。 添加或删除角色分配时，所做的更改可能需要长达30分钟的时间才能生效。 如果使用的是 Azure 门户、Azure PowerShell 或 Azure CLI，则可以通过注销和登录来强制刷新角色分配更改。 如果使用 REST API 调用进行角色分配更改，则可以通过刷新访问令牌来强制刷新。

如果要在管理组范围内添加或删除角色分配，并且该角色具有`DataActions`，则可能不会在数小时内更新数据平面上的访问权限。 这仅适用于管理组作用域和数据平面。

## <a name="web-app-features-that-require-write-access"></a>需要写访问权限的 Web 应用功能

如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 web 应用的**写**访问权限（参与者或所有者），并且在任何只读方案中都不可用。

* 命令（例如启动、停止等。）
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 访问发布凭据和其他机密（如应用设置和连接字符串）
* 流式处理日志
* 资源日志配置
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

* TLS/SSL 证书和绑定（可在同一资源组和地理位置中的站点之间共享 TLS/SSL 证书）  
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

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果为用户分配了 "[读者](built-in-roles.md#reader)" 角色，则他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)****。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置********。 若要访问这些功能，需要[参与者](built-in-roles.md#contributor)角色。

## <a name="next-steps"></a>后续步骤

- [来宾用户故障排除](role-assignments-external-users.md#troubleshoot)
- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [查看 Azure RBAC 更改的活动日志](change-history-report.md)
