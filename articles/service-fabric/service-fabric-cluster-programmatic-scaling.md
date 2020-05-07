---
title: Azure Service Fabric 编程缩放
description: 根据自定义触发器以编程方式缩减或扩展 Azure Service Fabric 群集
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bd7c57f3089115e4da861fc8fd20331ab92bc33e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787101"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>以编程方式缩放 Service Fabric 群集 

在 Azure 中运行的 Service Fabric 群集在虚拟机规模集的基础上构建。  [群集缩放](./service-fabric-cluster-scale-in-out.md)介绍如何手动缩放或使用自动缩放规则缩放 Service Fabric 群集。 本文介绍如何使用 fluent Azure 计算 SDK（更高级的方案）管理凭据和缩小或扩大群集。 有关概述，请阅读[以编程方式协调 Azure 缩放操作](service-fabric-cluster-scaling.md#programmatic-scaling)。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>管理凭据
编写服务来处理缩放的难题之一是，该服务必须能够在无需交互式登录的情况下访问虚拟机规模集资源。 如果缩放服务可修改自身的 Service Fabric 应用程序，则访问 Service Fabric 群集的过程就很轻松，但访问规模集则需要提供凭据。 若要登录，可以使用在 [Azure CLI](https://github.com/azure/azure-cli) 中创建的[服务主体](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)。

可以使用以下步骤创建服务主体：

1. 以有权访问虚拟机规模集的用户身份登录到 Azure CLI (`az login`)
2. 使用 `az ad sp create-for-rbac` 创建服务主体
    1. 记下 appId（在某些文档中称为“客户端 ID”）、名称、密码和租户供稍后使用。
    2. 还需要准备好订阅 ID（可使用 `az account list` 查看）

Fluent 计算库可以使用这些凭据进行登录，如下所示（请注意，`IAzure` 等核心 Fluent Azure 类型位于 [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) 包中）：

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

登录后，可通过 `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` 查询规模集实例计数。

## <a name="scaling-out"></a>扩大
使用 fluent Azure 计算 SDK，只需执行几次调用，就能将实例添加到虚拟机规模集 -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

也可以使用 PowerShell cmdlet 管理虚拟机规模集大小。 [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) 可以检索虚拟机规模集对象。 当前容量可通过 `.sku.capacity` 属性获得。 将容量更改为相应值后，可以使用 [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) 命令更新 Azure 中的虚拟机规模集。

手动添加节点时，添加规模集实例应该就能启动新的 Service Fabric 节点，因为规模集模板包含相应的扩展，可将新实例自动加入 Service Fabric 群集。 

## <a name="scaling-in"></a>缩减

缩减过程类似于扩展。实际的虚拟机规模集更改几乎是相同的。 但是，如前所述，Service Fabric 只会自动清理持久性为金级或银级的已删除节点。 因此，在持久性为铜级的节点中缩减时，需要与 Service Fabric 群集交互，以关闭要删除的节点，并删除其状态。

关闭节点的准备工作涉及查找要删除的节点（最近添加的虚拟机规模集实例）并将其停用。 虚拟机规模集实例按其添加顺序编号，因此，可以通过比较节点名称中的数字后缀（它与基础虚拟机规模集实例名称相匹配）来查找较新的节点。 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

找到要删除的节点后，可以使用相同的 `FabricClient` 实例和前面的 `IAzure` 实例来停用并删除该节点。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

与横向扩展一样，如果脚本方法更可取的话，也可以使用适用于修改虚拟机规模集容量的 PowerShell cmdlet。 删除虚拟机实例后，可删除 Service Fabric 节点状态。

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>后续步骤

要开始实现自己的自动缩放逻辑，请先熟悉以下概念和有用的 API：

- [手动或使用自动缩放规则缩放](./service-fabric-cluster-scale-in-out.md)
- [用于 .NET 的 Fluent Azure 管理库](https://github.com/Azure/azure-sdk-for-net/tree/Fluent)（与 Service Fabric 群集的底层虚拟机规模集交互时非常有用）
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)（与 Service Fabric 群集及其节点交互时非常有用）
