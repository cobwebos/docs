---
title: 在 Azure Service Fabric 中删除节点类型 | Microsoft Docs
description: 了解如何从在 Azure 中运行的 Service Fabric 群集删除节点类型。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969407"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>如何删除 Service Fabric 节点类型
本文介绍如何通过删除群集的现有节点类型来缩放 Azure Service Fabric 群集。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过删除节点类型（虚拟机规模集）及其所有节点来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 不建议经常使用此方法从生产群集中删除节点类型。 这是一个非常危险的命令，因为它会删除节点类型后的虚拟机规模集资源。 

## <a name="durability-characteristics"></a>持续性特征
使用 AzServiceFabricNodeType 时，安全性的优先级高于速度。 节点类型必须为银级或金级[持续性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，原因如下：
- 铜级未提供任何关于保存状态信息的保证。
- 银级和金级持续性阻止对规模集的任何更改。
- 金级还可控制规模集下的 Azure 更新。

Service Fabric 会“协调”基础更改和更新，以便数据不会丢失。 但是，如果删除具有青铜持久性的节点类型，则可能会丢失状态信息。 如果要删除主节点类型并且应用程序是无状态的，则可以接受铜牌。 在生产环境中运行有状态工作负载时，最低配置应为银级。 同样，对于生产方案，主节点类型应始终为银级或金级。

### <a name="more-about-bronze-durability"></a>有关铜级持续性的详细信息

删除铜级的节点类型时，节点类型中的所有节点都会立即关闭。 Service Fabric 不会阻止任何铜级节点的规模集更新，因此所有 VM 都会立即关闭。 如果这些节点上有任何有状态的内容，则数据将丢失。 现在，即使是无状态，Service Fabric 中的所有节点也会参与该更新，因此整个邻域可能会丢失，这可能会影响群集本身。

## <a name="remove-a-node-type"></a>删除节点类型

1. 在开始此过程之前，请先处理此必备组件。

    - 群集处于正常状态。
    - 删除节点类型后，仍然有足够的容量，例如。 要放置的节点数要求副本计数。

2. 移动具有放置约束的所有服务，以使用节点类型之外的节点类型。

    - 修改应用程序/服务清单，使其不再引用节点类型。
    - 部署更改。

    然后验证：
    - 上面修改的所有服务不再在属于该节点类型的节点上运行。
    - 所有服务都运行正常。

3. 将节点类型取消标记为非主节点类型（跳过非主节点类型）

    - 找到用于部署的 Azure 资源管理器模板。
    - 查找与 Service Fabric 部分中的节点类型相关的部分。
    - 将 isPrimary 属性更改为 false。 \* * 不要删除与此任务中的节点类型相关的部分。
    - 部署已修改的 Azure 资源管理器模板。 \* * 根据群集配置，此步骤可能需要一段时间。
    
    然后验证：
    - 门户中 Service Fabric 部分指示群集已准备就绪。
    - 群集处于正常状态。
    - 所有属于节点类型的节点都不被标记为种子节点。

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

    - 对于铜牌持续性，请等待所有节点进入禁用状态
    - 对于 "白银" 和 "黄金" 持续性，某些节点将进入禁用状态，其余节点将处于禁用状态。 检查节点处于禁用状态的 "详细信息" 选项卡（如果它们都处于 "正在禁用" 状态以确保对基础结构服务分区进行仲裁），则可以安全地继续操作。

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
    
    等待节点类型的所有节点均被标记为 "已关闭"。
    
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

    请等到所有节点都从群集中删除。 节点不应在 SFX 中显示。

7. 删除 Service Fabric 节中的节点类型。

    - 找到用于部署的 Azure 资源管理器模板。
    - 查找与 Service Fabric 部分中的节点类型相关的部分。
    - 删除与节点类型对应的部分。
    - 仅适用于银色和更高耐用性群集，更新模板中的群集资源，并配置运行状况策略，以忽略 fabric：/系统应用程序运行状况，方法是在群集资源 `properties` 下添加 `applicationDeltaHealthPolicies`，如下所示。 下面的策略应忽略现有错误，但不允许新的运行状况错误。 
 
 
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

    - 部署已修改的 Azure 资源管理器模板。 \* * 此步骤将需要一段时间，通常长达两个小时。 此升级会将设置更改为 InfrastructureService，因此需要重新启动节点。 在这种情况下 `forceRestart` 将被忽略。 
    参数 `upgradeReplicaSetCheckTimeout` 指定 Service Fabric 等待分区处于安全状态（如果尚未处于安全状态）的最长时间。 一旦安全检查通过某个节点上的所有分区，Service Fabric 将继续该节点上的升级。
    参数 `upgradeTimeout` 的值可以缩短到6小时，但应使用最大安全12小时。

    然后验证：
    - 门户中 Service Fabric 资源会显示 "就绪"。

8. 删除对与节点类型相关的资源的所有引用。

    - 找到用于部署的 Azure 资源管理器模板。
    - 从模板中删除虚拟机规模集以及与节点类型相关的其他资源。
    - 部署更改。

    然后：
    - 等待部署完成。

## <a name="next-steps"></a>后续步骤
- 了解有关群集[持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)的更多信息。
- 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
- 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。
