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
ms.date: 03/10/2017
ms.author: mikerou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 8d7052fabeb348b4bba744b43d9af78f058175a8
ms.lasthandoff: 03/17/2017


---

# <a name="scale-a-service-fabric-cluster-programmatically"></a>以编程方式缩放 Service Fabric 群集 

有关[群集缩放](./service-fabric-cluster-scale-up-down.md)的文档中介绍了在 Azure 中缩放 Service Fabric 群集的基础知识。 本文介绍如何基于虚拟机规模集构建 Service Fabric 群集，以及如何以手动方式或使用自动缩放规则来缩放该群集。 本文档探讨如何针对更高级的方案，以编程方式协调 Azure 缩放操作。 

## <a name="reasons-for-programmatic-scaling"></a>编程缩放的原因
在许多方案中，手动缩放或通过自动缩放规则缩放是合理的做法。 但在其他方案中，这种缩放方法可能不合适。 这些方法的潜在缺点包括：

- 手动缩放要求登录并显式请求缩放操作。 如果经常需要执行缩放操作或者执行该操作的时间不可预测，则这种缩放方法可能不是一个很好的解决方案。
- 当自动缩放规则从虚拟机规模集中删除某个实例时，它们不会从关联的 Service Fabric 群集中自动删除该节点的信息，除非节点类型的持久性级别达到了银级或金级。 由于自动缩放规则在规模集级别（而不是 Service Fabric 级别）工作，因此，自动缩放规则可能会在未正常关闭 Service Fabric 节点的情况下将其删除。 在执行缩减操作后，这种强行删除节点的方式会使 Service Fabric 节点保持“虚幻”状态。 个人（或服务）需要定期清理 Service Fabric 群集中已删除节点的状态。
  - 请注意，持久性级别达到金级或银级的节点类型会自动清理已删除的节点。  
- 尽管自动缩放规则支持[许多指标](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)，但指标集的规模仍然有限。 如果方案需要根据该集中未涵盖的某个指标进行缩放，则自动缩放规则可能不是一个适当的选项。

由于这些限制，我们可能想要实现其他自定义的自动缩放模型。 

## <a name="scaling-apis"></a>缩放 API
Azure API 可让应用程序以编程方式使用虚拟机规模集和 Service Fabric 群集。 如果现有的自动缩放选项不适用于你的方案，可通过这些 API 实现自定义的缩放逻辑。 

实现这种“定制”自动缩放功能的方法之一是，将一个新的无状态服务添加到 Service Fabric 应用程序来管理缩放操作。 在服务的 `RunAsync` 方法中，有一组触发器可以确定是否需要缩放（包括检查最大群集大小等参数，以及缩放减缓）。   

用于虚拟机规模集交互（检查当前虚拟机实例的数目以及对其进行修改）的 API 为 fluent [Azure 管理计算库](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/1.0.0-beta50)。 fluent 计算库提供一个易用的 API 来与虚拟机规模集交互。

若要与 Service Fabric 群集本身交互，可使用 [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)。

当然，缩放代码无需在群集中以服务的形式运行即可缩放。 `IAzure` 和 `FabricClient` 均可远程连接到其关联的 Azure 资源，因此，缩放服务可以单纯地是一个控制台应用程序，或者是从 Service Fabric 应用程序外部运行的 Windows 服务。 

## <a name="credential-management"></a>凭据管理
编写服务来处理缩放的难题之一是，该服务必须能够在无需交互式登录的情况下访问虚拟机规模集资源。 如果缩放服务可修改自身的 Service Fabric 应用程序，则访问 Service Fabric 群集的过程就很轻松，但访问规模集则需要提供凭据。 若要登录，可以使用在 [Azure CLI 2.0](https://github.com/azure/azure-cli) 中创建的[服务主体](https://github.com/Azure/azure-sdk-for-net/blob/Fluent/AUTH.md#creating-a-service-principal-in-azure)。

可以使用以下步骤创建服务主体：

1. 以有权访问虚拟机规模集的用户身份登录到 Azure CLI (`az login`)
2. 使用 `az ad sp create-for-rbac` 创建服务主体
    1. 记下 appId（在某些文档中称为“客户端 ID”）、名称、密码和租户供稍后使用。
    2. 还需要准备好订阅 ID（可使用 `az account list` 查看）

fluent 计算库可使用如下所示的凭据登录：

```C#
var credentials = AzureCredentials.FromServicePrincipal(AzureClientId, AzureClientKey, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
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

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = Math.Min(MaximumNodeCount, NodeCount.Value + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

**目前，有一个 [bug](https://github.com/Azure/azure-sdk-for-net/issues/2716) 会导致此代码无法正常工作**，但即将发布的 Microsoft.Azure.Management.Compute.Fluent 版本中已融入了修复程序，到时应可解决此问题。 该 bug 表现为，使用 fluent 计算 API 更改虚拟机规模集属性（如容量）时，规模集 Resource Manager 模板中的受保护设置将会丢失。 缺少这些设置（还有其他因素）会导致无法在新的虚拟机实例上正常设置 Service Fabric 服务。

暂时的解决方法是，从缩放服务调用 PowerShell cmdlet 来执行相同的更改（不过，如果使用这种方法，则必须安装 PowerShell 工具）：

```C#
using (var psInstance = PowerShell.Create())
{
    psInstance.AddScript($@"
        $clientId = ""{AzureClientId}""
        $clientKey = ConvertTo-SecureString -String ""{AzureClientKey}"" -AsPlainText -Force
        $Credential = New-Object -TypeName ""System.Management.Automation.PSCredential"" -ArgumentList $clientId, $clientKey
        Login-AzureRmAccount -Credential $Credential -ServicePrincipal -TenantId {AzureTenantId}
        
        $vmss = Get-AzureRmVmss -ResourceGroupName {ResourceGroup} -VMScaleSetName {NodeTypeToScale}
        $vmss.sku.capacity = {newCapacity}
        Update-AzureRmVmss -ResourceGroupName {ResourceGroup} -Name {NodeTypeToScale} -VirtualMachineScaleSet $vmss
    ");

    psInstance.Invoke();

    if (psInstance.HadErrors)
    {
        foreach (var error in psInstance.Streams.Error)
        {
            ServiceEventSource.Current.ServiceMessage(Context, $"ERROR adding node: {error.ToString()}");
        }
    }                
}
```

手动添加节点时，添加规模集实例应该就能启动新的 Service Fabric 节点，因为规模集模板包含相应的扩展，可将新实例自动加入 Service Fabric 群集。 

## <a name="scaling-in"></a>缩减

缩减过程类似于扩展。 实际的虚拟机规模集更改几乎是相同的。 但是，如前所述，Service Fabric 只会自动清理持久性为金级或银级的已删除节点。 因此，在持久性为铜级的节点中缩减时，需要与 Service Fabric 群集交互，以关闭要删除的节点，然后删除其状态。

准备关闭节点的过程涉及到查找要删除的节点删除（最近添加的节点）并停用它。 对于非种子节点，可通过比较 `NodeInstanceId` 找到更新的节点。 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

请注意，*种子*节点似乎并不始终遵循首先删除较大实例 ID 这一约定。

找到要删除的节点后，可以使用相同的 `FabricClient` 实例和前面的 `IAzure` 实例来停用并删除该节点。

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);

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
var newCapacity = Math.Max(MinimumNodeCount, NodeCount.Value - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

删除虚拟机实例后，可删除 Service Fabric 节点状态。

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

如前所述，在 [Azure/azure-sdk-for-net#2716](https://github.com/Azure/azure-sdk-for-net/issues/2716) 发布之前，需要解决 `IVirtualMachineScaleSet.Update()` 无法正常工作的问题。

## <a name="potential-drawbacks"></a>潜在的缺点

如前面的代码片段中所示，创建自己的缩放服务可以针对应用程序的缩放行为实现最大控制度和定制性。 在需要精确何时或者如何缩减或扩展应用程序的方案中，这种方法非常有效。 但是，这种控制也附带了代码复杂性方面的弊端。 使用这种方法意味着需要拥有缩放代码，而这并不是一个简单的任务。

应选择哪种 Service Fabric 缩放方法取决于具体的方案。 如果缩放过程不常见，则具备手动添加或删除节点的能力也许已足够。 在比较复杂的方案中，能够以编程方式缩放的自动缩放规则和 SDK 可用作强大的替代方法。

## <a name="next-steps"></a>后续步骤

若要开始实现你自己的自动缩放逻辑，请先熟悉以下概念和有用的 API：

- [手动或使用自动缩放规则缩放](./service-fabric-cluster-scale-up-down.md)
- [用于 .NET 的 Fluent Azure 管理库](https://github.com/Azure/azure-sdk-for-net/tree/Fluent)（与 Service Fabric 群集的底层虚拟机规模集交互时非常有用）
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)（与 Service Fabric 群集及其节点交互时非常有用）
