---
title: 监视管理租户中的委派更改
description: 了解如何监视从客户租户到管理租户的委派活动。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 322580cc3d0246f7a34e28cdae94da57fda070b5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985144"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>监视管理租户中的委派更改

作为服务提供商，您可能希望了解何时通过[Azure 委派的资源管理](../concepts/azure-delegated-resource-management.md)将客户订阅或资源组委派给租户，或者删除以前委派的资源。

在管理租户中[，Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)跟踪租户级别的委派活动。 此记录的活动包括所有客户租户添加或删除的任何授权。

本主题介绍监视向租户（跨所有客户）委派活动所需的权限，以及执行此操作的最佳做法。 它还包括一个示例脚本，该脚本显示了一种查询和报告此数据的方法。

> [!IMPORTANT]
> 所有这些步骤都必须在管理租户中执行，而不是在任何客户租户中执行。

## <a name="enable-access-to-tenant-level-data"></a>启用对租户级数据的访问

要访问租户级活动日志数据，必须在根作用域 （/） 中为帐户分配[监视读取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)的内置角色。 此分配必须由具有具有其他提升访问权限的全局管理员角色的用户执行。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全局管理员帐户的访问权限

要在根作用域 （/） 中分配角色，您需要具有具有提升访问权限的全局管理员角色。 仅当需要分配角色时，才应添加此提升的访问，然后在完成角色分配时删除。

有关添加和删除提升的详细说明，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

提升访问权限后，帐户将在 Azure 中具有"用户访问管理员"角色。该角色位于根作用域中。 此角色分配允许您查看目录中任何订阅或管理组中的所有资源并分配访问权限，以及在根作用域中分配角色。 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>创建新的服务主体帐户以访问租户级数据

提升访问权限后，可以将适当的权限分配给帐户，以便它可以查询租户级别的活动日志数据。 此帐户需要将[监视读取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)内置角色分配给管理租户的根作用域。

> [!IMPORTANT]
> 在根作用域中授予角色分配意味着相同的权限将应用于租户中的每个资源。

由于这是广泛的访问级别，我们建议您将此角色分配给服务主体帐户，而不是将角色分配给单个用户或组。 此外，我们建议以下最佳实践：

- [创建新的服务主体帐户](../../active-directory/develop/howto-create-service-principal-portal.md)仅用于此函数，而不是将此角色分配给用于其他自动化的现有服务主体。
- 确保此服务主体无法访问任何委派的客户资源。
- [使用证书对其进行身份验证](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)[并将其安全地存储在 Azure 密钥保管库中](../../key-vault/key-vault-best-practices.md)。
- 限制有权访问代表服务主体行事的用户。

使用以下方法之一进行根作用域分配。

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>删除全局管理员帐户的提升访问权限

创建服务主体帐户并在根作用域中分配监视读取器角色后，请确保删除全局管理员帐户的[提升访问权限](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)，因为不再需要此级别的访问。

## <a name="query-the-activity-log"></a>查询活动日志

创建具有监视读取器访问权限的新服务主体帐户后，可以使用该帐户查询和报告租户中的委派活动。 

[此 Azure PowerShell 脚本](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)可用于查询过去 1 天的活动，并报告任何添加或删除的代表团（或未成功的尝试）。 它查询[租户活动日志](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List)数据，然后构造以下值以报告添加或删除的代表团：

- **委派资源 Id：** 委派的订阅或资源组的 ID
- **客户租户 ID**：客户租户 ID
- **客户订阅 ID**：已委派或包含委派的资源组的订阅 ID
- **客户委派状态**：委派资源的状态更改（成功或失败）
- **事件时间戳**：记录委派更改的日期和时间

查询此数据时，请记住：

- 如果在单个部署中委派了多个资源组，则将为每个资源组返回单独的条目。
- 对以前的委派所做的更改（如更新权限结构）将作为附加委派记录。
- 如上所述，帐户必须在根作用域 （/） 具有监视读取器内置角色才能访问此租户级别数据。
- 您可以在自己的工作流和报告中使用此数据。 例如，可以使用[HTTP 数据收集器 API（公共预览）](../../azure-monitor/platform/data-collector-api.md)从 REST API 客户端将数据记录到 Azure 监视器，然后使用[操作组](../../azure-monitor/platform/action-groups.md)创建通知或警报。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>后续步骤

- 了解如何将客户添加到 Azure[委派的资源管理](../concepts/azure-delegated-resource-management.md)。
- 了解[Azure 监视器](../../azure-monitor/index.yml)和[Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)。
