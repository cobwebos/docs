---
title: 将 Azure Policy 大规模部署到委托订阅
description: 了解 Azure 委托资源管理如何允许跨多个租户部署策略定义和策略分配。
ms.date: 11/8/2019
ms.topic: overview
ms.openlocfilehash: fd335e77feb26241d573db48c2e96c725f70d031
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131269"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>将 Azure Policy 大规模部署到委托订阅

作为服务提供商，你可能已经载入了多个客户租户进行 Azure 委派资源管理。 [Azure Lighthouse](../overview.md) 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题说明如何按照 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/) 使用 PowerShell 命令在多个租户中部署策略定义和策略分配。 在此示例中，策略定义确保通过允许仅 HTTPS 流量来保护存储帐户。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客户租户之间执行查询

你可以使用 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/) 在所管理的客户租户的所有订阅中执行查询。 在此示例中，我们将在这些订阅中识别当前不需要 HTTPS 流量的所有存储帐户。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>跨多个客户租户部署策略

以下示例演示如何使用 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json)跨多个客户租户中的委托订阅部署策略定义和策略分配。 此策略定义要求所有存储帐户都使用 HTTPS 流量，防止创建任何不符合要求的新存储帐户并在没有设置为不符合的情况下标记现有存储帐户。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>验证策略部署

部署 Azure 资源管理器模板之后，可以通过尝试创建存储帐户，并在其中一个委托订阅中将 EnableHttpsTrafficOnly  设置为 false  来确认策略定义已成功应用。 由于策略分配，你应该无法创建此存储帐户。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清理资源

完成后，删除部署所创建的策略定义和分配。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
