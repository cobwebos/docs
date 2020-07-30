---
title: 排查 Azure RBAC 的问题
description: 排查 Azure 基于角色的访问控制 (Azure RBAC) 的问题。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 839662e496a61ff9a90a6250b417688b91ccaed1
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382570"
---
# <a name="troubleshoot-azure-rbac"></a>排查 Azure RBAC 的问题

本文解答有关 Azure 基于角色的访问控制 (Azure RBAC) 的一些常见问题，使你能够了解在使用角色时可能出现的情况，并排查访问问题。

## <a name="azure-role-assignments-limit"></a>Azure 角色分配限制

Azure 对于每个订阅最多支持 **2000** 个角色分配。 此限制包括订阅、资源组和资源范围内的角色分配。 如果尝试分配角色时收到错误消息“无法创建更多的角色分配(代码:RoleAssignmentLimitExceeded)”，请尝试减少订阅中的角色分配数。

> [!NOTE]
> 每个订阅的角色分配数限制 2000 是固定的，无法提高。

如果即将达到此限制，可通过以下方式减少角色分配数：

- 将用户添加到组，并改为向组分配角色。 
- 结合使用多个内置角色和一个自定义角色。 
- 在较高的范围（例如订阅或管理组）进行常见的角色分配。
- 如果你有 Azure AD Premium P2，请在 [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) 中创建符合条件的角色分配，而不要永久分配角色。 
- 添加额外的订阅。 

若要获取角色分配数，可以查看 Azure 门户中[“访问控制(IAM)”页上的图表](role-assignments-list-portal.md#list-number-of-role-assignments)。 还可以使用以下 Azure PowerShell 命令：

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure 角色分配问题

- 如果你因为“添加” > “添加角色分配”选项被禁用或者因为收到权限错误“具有此对象 id 的客户端无权执行操作”而无法在 Azure 门户中的“访问控制(IAM)”上添加角色分配，请检查你当前登录时使用的用户是否为在你尝试分配角色的范围中具有 `Microsoft.Authorization/roleAssignments/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果要使用服务主体来分配角色，可能会收到错误 "权限不足，无法完成操作"。 例如，假设你有一个服务主体，该服务主体已被分配为 "所有者" 角色，并且你尝试使用 Azure CLI 创建以下角色分配作为服务主体：

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    如果收到错误 "权限不足，无法完成操作"，则很可能是因为 Azure CLI 尝试在 Azure AD 中查找工作负责人标识，并且服务主体在默认情况下无法读取 Azure AD。

    有两种方法可以解决此错误。 第一种方法是将[目录读取器](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色分配给服务主体，以便它能够读取目录中的数据。

    解决此错误的第二种方法是使用 `--assignee-object-id` 参数而不是创建角色分配 `--assignee` 。 使用 `--assignee-object-id` 会跳过 Azure AD 查找 Azure CLI。 需要获取要为其分配角色的用户、组或应用程序的对象 ID。 有关详细信息，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](role-assignments-cli.md#new-service-principal)。

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

## <a name="problems-with-custom-roles"></a>自定义角色出现问题

- 如需了解创建自定义角色的步骤，请参阅使用 [Azure 门户](custom-roles-portal.md)（目前为预览版功能）、[Azure PowerShell](tutorial-custom-role-powershell.md) 或 [Azure CLI](tutorial-custom-role-cli.md) 创建自定义角色的教程。
- 如果你无法更新现有的自定义角色，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Authorization/roleDefinition/write` 权限的角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)。
- 如果你无法删除自定义角色并且收到错误消息“已存在引用此角色的角色分配(代码:RoleDefinitionHasAssignments)”，则表明存在仍然使用此自定义角色的角色分配。 请删除这些角色分配，然后再次尝试删除自定义角色。
- 如果尝试创建新的自定义角色时收到错误消息“角色定义超限。 无法创建更多的角色定义(代码:RoleDefinitionLimitExceeded)”，请删除未在使用的任何自定义角色。 Azure 在一个目录中最多支持 5000 个自定义角色。 （对于 Azure 德国云和 Azure 中国世纪互联，限制为 2000 个自定义角色。）
- 如果尝试更新自定义角色时收到类似于“客户端具有在范围 '/subscriptions/{subscriptionid}' 上执行操作 'Microsoft.Authorization/roleDefinitions/write' 的权限，但是未找到链接的订阅”的错误，请检查是否已在目录中删除了一个或多个[可分配的范围](role-definitions.md#assignablescopes)。 如果删除了作用域，请创建一个支持票证，因为目前没有自助服务解决方案可用。

## <a name="custom-roles-and-management-groups"></a>自定义角色和管理组

- 只能在自定义角色的 `AssignableScopes` 中定义一个管理组。 将管理组添加到 `AssignableScopes` 的功能目前为预览版。
- 无法在管理组范围内分配具有 `DataActions` 的自定义角色。
- Azure 资源管理器不验证管理组是否存在于角色定义的可分配范围中。
- 若要详细了解自定义角色和管理组，请参阅[使用 Azure 管理组来组织资源](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)。

## <a name="transferring-a-subscription-to-a-different-directory"></a>将订阅转移到另一目录

- 如需了解将订阅转移到另一 Azure AD 目录的步骤，请参阅[将 Azure 订阅所有权转移到另一帐户](../cost-management-billing/manage/billing-subscription-transfer.md)。
- 如果将订阅转移到另一 Azure AD 目录，所有角色分配将从源 Azure AD 目录中永久删除，而不会迁移到目标 Azure AD 目录。 必须在目标目录中重新创建角色分配。 此外，还需手动重新创建 Azure 资源的托管标识。 有关详细信息，请参阅[托管标识的 FAQ 和已知问题](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果你是 Azure AD 全局管理员并且在目录之间转移某个订阅后对其没有访问权限，请使用“Azure 资源的访问权限管理”开关暂时[提升你的访问权限](elevate-access-global-admin.md)来获取对订阅的访问权限。

## <a name="issues-with-service-admins-or-co-admins"></a>服务管理员或共同管理员出现问题

- 如果遇到服务管理员或共同管理员方面的问题，请参阅[添加或更改 Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)以及[经典订阅管理员角色、Azure 角色和 Azure AD 角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>访问被拒绝或权限错误

- 如果尝试创建资源时收到权限错误“具有此对象 id 的客户端无权在此作用域内执行操作(代码:AuthorizationFailed)”，请检查你当前登录时使用的用户是否分配有在所选作用域内对资源具有写入权限的角色。 例如，若要管理某个资源组中的虚拟机，则你应当在该资源组（或父作用域）中具有[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色。 有关每个内置角色的权限列表，请参阅 [Azure 内置角色](built-in-roles.md)。
- 如果尝试创建或更新支持票证时收到权限错误“无权创建支持票证”，请检查你当前登录时使用的用户是否分配有具有 `Microsoft.Support/supportTickets/write` 权限的角色，例如[支持请求参与者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-with-identity-not-found"></a>未找到标识的角色分配

在 Azure 门户的角色分配列表中，你可能会注意到安全主体（用户、组、服务主体或托管标识）列为“未找到标识”，类型为“未知” 。

![Web 应用程序资源组](./media/troubleshooting/unknown-security-principal.png)

找不到标识的原因有两个：

- 你最近在创建角色分配时邀请了用户
- 你删除了具有角色分配的安全主体

如果你最近在创建角色分配时邀请了用户，则此安全主体可能仍处于跨区域的复制过程中。 如果是这样，请稍等片刻并刷新角色分配列表。

但是，如果此安全主体不是最近邀请的用户，则可能是已删除的安全主体。 如果将角色分配给安全主体，然后在不先删除角色分配的情况下删除该安全主体，则该安全主体类型将列为“未找到标识”，类型为“未知” 。

如果使用 Azure PowerShell 列出此角色分配，则可能看到空的 `DisplayName` 且 `ObjectType` 设置为“未知”。 例如，[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 返回类似于以下输出的角色分配：

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

同样，如果使用 Azure CLI 列出此角色分配，则可能看到空的 `principalName`。 例如，[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) 返回类似于以下输出的角色分配：

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

在删除安全主体的情况下，保留这些角色分配没有问题。 如果需要，可以使用与其他角色分配相似的步骤删除这些角色分配。 有关如何删除角色分配的信息，请参阅 [Azure 门户](role-assignments-portal.md#remove-a-role-assignment)、[Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment) 或 [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

在 PowerShell 中，如果尝试通过对象 ID 和角色定义名称来删除角色分配，而多个角色分配与参数相匹配，则会出现错误消息：“提供的信息未映射到角色分配”。 以下输出显示了错误消息示例：

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

如果在管理组范围添加或删除某个角色分配，并且该角色具有 `DataActions`，对数据平面的访问权限在几个小时内可能不会更新。 这仅适用于管理组范围和数据平面。

## <a name="web-app-features-that-require-write-access"></a>需要写访问权限的 Web 应用功能

如果为用户授予单个 Web 应用的只读访问权限，某些功能可能会被禁用，这可能不是你所期望的。 以下管理功能需要对 Web 应用具有**写**访问权限（参与者或所有者），并且在任何只读方案中不可用。

* 命令（例如启动、停止等。）
* 更改设置（如常规配置、缩放设置、备份设置和监视设置）
* 访问发布凭据和其他机密（如应用设置和连接字符串）
* 流式处理日志
* 资源日志配置
* 控制台（命令提示符）
* 活动和最新部署（适用于本地 Git 持续部署）
* 估计费用
* Web 测试
* 虚拟网络（只在虚拟网络是由具有写入权限的用户在以前配置时，才对读者可见）。

如果无法访问以上任何磁贴，则需要让管理员提供对 Web 应用的“参与者”访问权限。

## <a name="web-app-resources-that-require-write-access"></a>需要写访问权限的 Web 应用资源

由于存在几个相互作用的不同资源，Web 应用程序是复杂的。 下面是包含几个网站的典型资源组：

![Web 应用程序资源组](./media/troubleshooting/website-resource-model.png)

因此，如果只授予某人对 Web 应用的访问权限，则 Azure 门户中的网站边栏选项卡上的很多功能将被禁用。

这些项需要对与网站对应的应用服务计划具有写入权限：   

* 查看 Web 应用的定价层（“免费”或“标准”）  
* 规模配置（实例数、虚拟机大小、自动缩放设置）  
* 配额（存储空间、带宽、CPU）  

这些项需要对包含网站的整个资源组具有写入权限：   

* TLS/SSL 证书和绑定（TLS/SSL 证书可以在同一资源组和地理位置中的站点之间共享）  
* 警报规则  
* 自动缩放设置  
* Application Insights 组件  
* Web 测试  

## <a name="virtual-machine-features-that-require-write-access"></a>需要写访问权限的虚拟机功能

与 Web 应用类似，虚拟机边栏选项卡上的某些功能需要对虚拟机或资源组中的其他资源具有写访问权限。

虚拟机与域名、虚拟网络、存储帐户和警报规则相关。

这些项需要对虚拟机具有写入权限： 

* 终结点  
* IP 地址  
* 磁盘  
* 扩展  

这些项需要对虚拟机和其所在的资源组（以及域名）具有写入权限：    

* 可用性集  
* 负载均衡集  
* 警报规则  

如果无法访问以上任何磁贴，则需要让管理员提供对资源组的“参与者”访问权限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和写访问权限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要写入权限。 例如，如果给用户分配[读者](built-in-roles.md#reader)角色，他们将无法查看函数应用中的函数。 门户将显示 (无访问权限)。

![函数应用无访问权限](./media/troubleshooting/functionapps-noaccess.png)

读者可单击“平台功能”选项卡，然后单击“所有设置”查看与函数应用（类似于 Web 应用）相关的一些设置，但无法修改任何这些设置 。 若要访问这些功能，需要[参与者](built-in-roles.md#contributor)角色。

## <a name="next-steps"></a>后续步骤

- [来宾用户疑难解答](role-assignments-external-users.md#troubleshoot)
- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [查看 Azure RBAC 更改的活动日志](change-history-report.md)
