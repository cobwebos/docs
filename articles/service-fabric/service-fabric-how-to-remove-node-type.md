---
title: 在 Azure Service Fabric 中删除节点类型 | Microsoft Docs
description: 了解如何从在 Azure 中运行的 Service Fabric 群集删除节点类型。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969407"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>如何删除服务结构节点类型
本文介绍如何通过删除群集的现有节点类型来缩放 Azure Service Fabric 群集。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过删除节点类型（虚拟机规模集）及其所有节点来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 不建议经常使用此方法从生产群集中删除节点类型。 这是一个非常危险的命令，因为它会删除节点类型后的虚拟机规模集资源。 

## <a name="durability-characteristics"></a>持续性特征
使用 Remove-AzServiceFabricNodeType 时，安全性优先于速度。 节点类型必须为银级或金级[持续性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，原因如下：
- 铜级未提供任何关于保存状态信息的保证。
- 银级和金级持续性阻止对规模集的任何更改。
- 金级还可控制规模集下的 Azure 更新。

Service Fabric 会“协调”基础更改和更新，以便数据不会丢失。 但是，当您删除具有青铜持久性的节点类型时，可能会丢失状态信息。 如果要删除主节点类型，并且应用程序处于无状态状态，则"铜牌"是可以接受的。 在生产环境中运行有状态工作负载时，最低配置应为银级。 同样，对于生产方案，主节点类型应始终为银级或金级。

### <a name="more-about-bronze-durability"></a>有关铜级持续性的详细信息

删除铜级的节点类型时，节点类型中的所有节点都会立即关闭。 Service Fabric 不会阻止任何铜级节点的规模集更新，因此所有 VM 都会立即关闭。 如果这些节点上有任何有状态的内容，则数据将丢失。 现在，即使是无状态，Service Fabric 中的所有节点也会参与该更新，因此整个邻域可能会丢失，这可能会影响群集本身。

## <a name="remove-a-node-type"></a>删除节点类型

1. 在开始此过程之前，请注意此先决条件。

    - 群集是正常运行的。
    - 删除节点类型后，仍有足够的容量，例如。 要放置所需副本计数的节点数。

2. 将所有具有放置约束的服务从节点类型中移出节点类型。

    - 修改应用程序/服务清单以不再引用节点类型。
    - 部署更改。

    然后验证：
    - 上面修改的所有服务不再在属于节点类型的节点上运行。
    - 所有服务都是健康的。

3. 取消将节点类型标记为非主节点类型（为非主节点类型跳过）

    - 查找用于部署的 Azure 资源管理器模板。
    - 在"服务结构"部分中查找与节点类型相关的部分。
    - 将主要属性更改为 false。 • 不要删除与此任务中的节点类型相关的部分。
    - 部署修改后的 Azure 资源管理器模板。 • 根据群集配置，此步骤可能需要一段时间。
    
    然后验证：
    - 门户中的服务结构部分指示群集已准备就绪。
    - 群集是健康的。
    - 不属于节点类型的节点均未标记为种子节点。

4. 禁用节点类型的数据。

    使用 PowerShell 连接到群集，然后运行以下步骤。
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - 对于青铜持久性，请等待所有节点进入禁用状态
    - 对于白银和黄金的持久性，某些节点将处于禁用状态，其余节点将处于禁用状态。 检查处于禁用状态的节点的详细信息选项卡，如果它们都停留在确保基础结构服务分区的仲裁上，则可以安全地继续。

5. 停止节点类型的数据。

    使用 PowerShell 连接到群集，然后运行以下步骤。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    等待，直到节点类型的所有节点都标记为"向下"。
    
6. 删除节点类型的数据。

    使用 PowerShell 连接到群集，然后运行以下步骤。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    等待，直到从群集中删除所有节点。 节点不应显示在 SFX 中。

7. 从服务结构部分删除节点类型。

    - 查找用于部署的 Azure 资源管理器模板。
    - 在"服务结构"部分中查找与节点类型相关的部分。
    - 删除与节点类型对应的部分。
    - 仅适用于 Silver 和更高持久性群集，更新模板中的群集资源，并将运行状况策略配置为忽略结构：/系统应用程序运行状况，只需在下面`applicationDeltaHealthPolicies`给出的群集`properties`资源下添加。 以下策略应忽略现有错误，但不允许出现新的运行状况错误。 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - 部署修改后的 Azure 资源管理器模板。 • 此步骤需要一段时间，通常长达两个小时。 此升级将更改基础结构服务的设置，因此需要重新启动节点。 在这种情况下`forceRestart`将被忽略。 
    该参数`upgradeReplicaSetCheckTimeout`指定 Service Fabric 等待分区处于安全状态（如果不是尚未处于安全状态）的最大时间。 一旦对节点上的所有分区进行安全检查，Service Fabric 将继续升级该节点。
    参数`upgradeTimeout`的值可以减小到 6 小时，但为了最大安全，应使用 12 小时。

    然后验证：
    - 门户中的服务结构资源已准备就绪。

8. 删除对与节点类型相关的资源的所有引用。

    - 查找用于部署的 Azure 资源管理器模板。
    - 从模板中删除虚拟机缩放集以及与节点类型相关的其他资源。
    - 部署更改。

    然后：
    - 等待部署完成。

## <a name="next-steps"></a>后续步骤
- 了解有关群集[持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)的更多信息。
- 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
- 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。
