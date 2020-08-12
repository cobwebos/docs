---
title: 监视管理租户中的委派更改
description: 了解如何监视从客户租户到管理租户的委托活动。
ms.date: 08/11/2020
ms.topic: how-to
ms.openlocfilehash: 9842ad91c059fe4da70221d8c7c5570084bcc6b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119005"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>监视管理租户中的委派更改

作为服务提供商，你可能想要知道客户订阅或资源组通过[Azure Lighthouse](../overview.md)委托给你的租户，或者在删除之前委派的资源时。

在管理租户中， [Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)在租户级别跟踪委托活动。 此记录的活动包含所有客户租户中添加或删除的委派。

本主题介绍在所有客户) 中监视租户 (所需的权限，以及执行此操作的最佳做法。 它还包含一个示例脚本，其中显示了用于查询和报告此数据的一种方法。

> [!IMPORTANT]
> 所有这些步骤都必须在管理租户中执行，而不是在任何客户租户中执行。

## <a name="enable-access-to-tenant-level-data"></a>启用对租户级别数据的访问

若要访问租户级别的活动日志数据，必须在根范围 (/) 向帐户分配[监视读取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)Azure 内置角色。 此分配必须由具有 "全局管理员" 角色的用户执行，并且具有其他提升的访问权限。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全局管理员帐户的访问权限

若要在根范围 (/) 中分配角色，需要具有提升的访问权限的全局管理员角色。 仅当需要进行角色分配时才应添加此提升的访问权限，并在完成后将其删除。

有关添加和删除提升的详细说明，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

提升访问权限后，你的帐户将在 Azure 中具有根范围内的用户访问管理员角色。 此角色分配允许你查看所有资源并在目录中的任何订阅或管理组中分配访问权限，以及在根范围内分配角色。

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>创建新的服务主体帐户以访问租户级别的数据

提升访问权限后，可以为帐户分配适当的权限，以便它可以查询租户级别的活动日志数据。 此帐户需要在管理租户的根范围内分配[监视读取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)Azure 内置角色。

> [!IMPORTANT]
> 在根范围授予角色分配意味着相同的权限将应用于租户中的每个资源。

由于这是一种广泛的访问级别，因此建议将此角色分配给服务主体帐户，而不是分配给单个用户或组。

 此外，我们建议采用以下最佳做法：

- [创建新的服务主体帐户](../../active-directory/develop/howto-create-service-principal-portal.md)以便仅用于此函数，而不是将此角色分配到用于其他自动化的现有服务主体。
- 确保此服务主体不具有对任何委派的客户资源的访问权限。
- [使用证书进行身份验证](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)，并[将其安全地存储在 Azure Key Vault 中](../../key-vault/general/best-practices.md)。
- 限制有权代表服务主体执行操作的用户。

使用以下方法之一来进行根范围分配。

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

> [!NOTE]
> 你还可以将监视读取器 Azure 内置角色的根范围分配给单个用户或用户组。 如果希望用户能够[直接在 Azure 门户中查看委派信息](#view-delegation-changes-in-the-azure-portal)，这会很有用。 如果这样做，请注意，这是一种广泛的访问级别，应限制为尽可能少的用户数。

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>删除全局管理员帐户的提升的访问权限

在创建了服务主体帐户并在根范围内分配了 "监视读取者" 角色后，请确保删除全局管理员帐户的[提升的访问权限](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)，因为不再需要此级别的访问权限。

## <a name="query-the-activity-log"></a>查询活动日志

一旦你创建了一个新的服务主体帐户，该帐户具有对管理租户的根作用域的 "监视读取者" 访问权限，你就可以使用它来查询和报告租户中的委托活动。

[此 Azure PowerShell 脚本](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)可用于查询过去1天的活动，并报告任何已添加或已删除的委派 (或未成功) 的尝试。 它将查询[租户活动日志](/rest/api/monitor/TenantActivityLogs/List)数据，然后构造以下值以报告添加或删除的委托：

- **DelegatedResourceId**：委派的订阅或资源组的 ID
- **CustomerTenantId**：客户租户 ID
- **CustomerSubscriptionId**：已委派或包含已委派的资源组的订阅 ID
- **CustomerDelegationStatus**：委派的资源的状态更改 (succeeded 或 failed) 
- **EventTimeStamp**：记录委派更改的日期和时间

查询此数据时，请记住：

- 如果在单个部署中委派了多个资源组，则将为每个资源组返回单独的条目。
- 对以前的委派 (所做的更改（例如更新权限结构) 将记录为添加的委派。
- 如上所述，帐户必须具有监视读取器 Azure 内置角色（在根范围内） (/) 才能访问此租户级别的数据。
- 您可以在自己的工作流和报告中使用此数据。 例如，可以使用[HTTP 数据收集器 API (公共预览版) ](../../azure-monitor/platform/data-collector-api.md)将数据从 REST API 客户端记录到 Azure Monitor，然后使用[操作组](../../azure-monitor/platform/action-groups.md)创建通知或警报。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="view-delegation-changes-in-the-azure-portal"></a>查看 Azure 门户中的委派更改

在根范围内分配了监视读者 Azure 内置角色的用户可以直接在 Azure 门户中查看委托更改。

1. 导航到 "**我的客户**" 页，然后从左侧导航菜单中选择 "**活动日志**"。
1. 确保在屏幕顶部附近的筛选器中选择 "**目录活动**"。

将显示委托更改列表。 你可以选择 **"编辑列**" 以显示或隐藏**状态**、**事件类别**、**时间**、**时间戳**、**订阅**、**事件启动者**、**资源组**、**资源类型**和**资源**值。

## <a name="next-steps"></a>后续步骤

- 了解如何将客户加入[Azure Lighthouse](../concepts/azure-delegated-resource-management.md)。
- 了解[Azure Monitor](../../azure-monitor/index.yml)和[Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)。
