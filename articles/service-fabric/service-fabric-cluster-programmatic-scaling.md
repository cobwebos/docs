---
title: "Azure Service Fabric 编程缩放 | Microsoft Docs"
description: "根据自定义触发器以编程方式缩减或扩展 Azure Service Fabric 群集"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bfa020e29a9bb67f0634d220725bc11279e1565c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>以编程方式缩放 Service Fabric 群集 

有关[群集缩放](./service-fabric-cluster-scale-up-down.md)的文档中介绍了在 Azure 中缩放 Service Fabric 群集的基础知识。 本文介绍如何基于虚拟机规模集构建 Service Fabric 群集，以及如何以手动方式或使用自动缩放规则来缩放该群集。 本文档探讨如何针对更高级的方案，以编程方式协调 Azure 缩放操作。 

## <a name="reasons-for-programmatic-scaling"></a>编程缩放的原因
在许多方案中，手动缩放或通过自动缩放规则缩放是合理的做法。 但在其他方案中，这种缩放方法可能不合适。 这些方法的潜在缺点包括：

- 手动缩放要求登录并显式请求缩放操作。 如果经常需要执行缩放操作或者执行该操作的时间不可预测，则这种缩放方法可能不是一个很好的解决方案。
- 当自动缩放规则从虚拟机规模集中删除某个实例时，它们不会从关联的 Service Fabric 群集中自动删除该节点的信息，除非节点类型的持久性级别达到了银级或金级。 由于自动缩放规则在规模集级别（而不是 Service Fabric 级别）工作，因此，自动缩放规则可能会在未正常关闭 Service Fabric 节点的情况下将其删除。 在执行缩减操作后，这种强行删除节点的方式会使 Service Fabric 节点保持“虚幻”状态。 个人（或服务）需要定期清理 Service Fabric 群集中已删除节点的状态。
  - 持久性级别达到金级或银级的节点类型会自动清理已删除的节点，因此无需任何附加清理。
- 尽管自动缩放规则支持[许多指标](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)，但指标集的规模仍然有限。 如果方案需要根据该集中未涵盖的某个指标进行缩放，则自动缩放规则可能不是一个适当的选项。

由于这些限制，我们可能想要实现其他自定义的自动缩放模型。 

## <a name="scaling-apis"></a>缩放 API
Azure API 可让应用程序以编程方式使用虚拟机规模集和 Service Fabric 群集。 如果现有的自动缩放选项不适用于方案，可通过这些 API 实现自定义的缩放逻辑。 

实现这种“定制”自动缩放功能的方法之一是，将一个新的无状态服务添加到 Service Fabric 应用程序来管理缩放操作。 在服务的 `RunAsync` 方法中，有一组触发器可以确定是否需要缩放（包括检查最大群集大小等参数，以及缩放减缓）。   

适用于虚拟机规模集交互的 API（用于确定和修改当前虚拟机实例数量）为 [Fluent Azure 管理计算库](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/)。 fluent 计算库提供一个易用的 API 来与虚拟机规模集交互。

若要与 Service Fabric 群集本身交互，可使用 [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)。

当然，缩放代码无需在群集中以服务的形式运行即可缩放。 `IAzure` 和 `FabricClient` 均可远程连接到其关联的 Azure 资源，因此，缩放服务可以单纯地是一个控制台应用程序，或者是从 Service Fabric 应用程序外部运行的 Windows 服务。 

## <a name="credential-management"></a>凭据管理
编写服务来处理缩放的难题之一是，该服务必须能够在无需交互式登录的情况下访问虚拟机规模集资源。 如果缩放服务可修改自身的 Service Fabric 应用程序，则访问 Service Fabric 群集的过程就很轻松，但访问规模集则需要提供凭据。 若要登录，可以使用在 [Azure CLI 2.0](https://github.com/azure/azure-cli) 中创建的[服务主体](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)。

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

也可以使用 PowerShell cmdlet 管理虚拟机规模集大小。 [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) 可以检索虚拟机规模集对象。 当前容量可通过 `.sku.capacity` 属性获得。 将容量更改为相应值后，可以使用 [`Update-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) 命令更新 Azure 中的虚拟机规模集。

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

## <a name="potential-drawbacks"></a>潜在的缺点

如前面的代码片段中所示，创建自己的缩放服务可以针对应用程序的缩放行为实现最大控制度和定制性。 在需要精确何时或者如何缩减或扩展应用程序的方案中，这种方法非常有效。但是，这种控制也附带了代码复杂性方面的弊端。 使用这种方法意味着需要拥有缩放代码，而这并不是一个简单的任务。

应选择哪种 Service Fabric 缩放方法取决于具体的方案。 如果缩放过程不常见，则具备手动添加或删除节点的能力也许已足够。 在比较复杂的方案中，能够以编程方式缩放的自动缩放规则和 SDK 可用作强大的替代方法。

## <a name="next-steps"></a>后续步骤

要开始实现自己的自动缩放逻辑，请先熟悉以下概念和有用的 API：

- [手动或使用自动缩放规则缩放](./service-fabric-cluster-scale-up-down.md)
- [用于 .NET 的 Fluent Azure 管理库](https://github.com/Azure/azure-sdk-for-net/tree/Fluent)（与 Service Fabric 群集的底层虚拟机规模集交互时非常有用）
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)（与 Service Fabric 群集及其节点交互时非常有用）
