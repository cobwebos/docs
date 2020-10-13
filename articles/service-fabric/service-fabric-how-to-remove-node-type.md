---
title: 在 Azure Service Fabric 中删除节点类型 | Microsoft Docs
description: 了解如何从在 Azure 中运行的 Service Fabric 群集删除节点类型。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163571"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>如何删除 Service Fabric 节点类型
本文介绍如何通过删除群集的现有节点类型来缩放 Azure Service Fabric 群集。 Service Fabric 群集是通过网络连接在一起的一组虚拟机或物理机，可在其中部署和管理微服务。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过删除节点类型（虚拟机规模集）及其所有节点来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 建议不要频繁使用此方法从生产群集中删除节点类型。 这是一个非常危险的命令，因为它会删除节点类型后的虚拟机规模集资源。 

## <a name="durability-characteristics"></a>持续性特征
使用 Remove-AzServiceFabricNodeType 时，安全性优先于速度。 节点类型必须为银级或金级[持续性级别](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)，原因如下：
- 铜级未提供任何关于保存状态信息的保证。
- 银级和金级持续性阻止对规模集的任何更改。
- 金级还可控制规模集下的 Azure 更新。

Service Fabric 会“协调”基础更改和更新，以便数据不会丢失。 但是，当删除具有铜级持续性的节点类型时，可能会丢失状态信息。 如果要删除主节点类型且应用程序是无状态的，则铜级是可接受的。 在生产环境中运行有状态工作负载时，最低配置应为银级。 同样，对于生产方案，主节点类型应始终为银级或金级。

### <a name="more-about-bronze-durability"></a>有关铜级持续性的详细信息

删除铜级的节点类型时，节点类型中的所有节点都会立即关闭。 Service Fabric 不会阻止任何铜级节点的规模集更新，因此所有 VM 都会立即关闭。 如果这些节点上有任何有状态的内容，则数据将丢失。 现在，即使是无状态，Service Fabric 中的所有节点也会参与该更新，因此整个邻域可能会丢失，这可能会影响群集本身。

## <a name="remove-a-node-type"></a>删除节点类型

1. 在开始执行此过程之前，请先满足以下先决条件。

    - 群集正常运行。
    - 删除该节点类型后，仍有足够的容量，例如， 有足够数量的节点可用来放置所需数目的副本。

2. 移动其放置约束要求使用该节点类型的所有服务。

    - 修改应用程序/服务清单，以不再引用该节点类型。
    - 部署更改。

    然后验证以下事项：
    - 上面修改的所有服务不再在属于该节点类型的节点上运行。
    - 所有服务正常运行。

3. 取消将节点类型标记为非主要节点类型（对于非主要节点类型，请跳过此步骤）

    - 找到用于部署的 Azure 资源管理器模板。
    - 在“Service Fabric”部分中找到与该节点类型相关的部分。
    - 将 isPrimary 属性更改为 false。 ** 不要在此任务中删除与该节点类型相关的部分。
    - 部署修改后的 Azure 资源管理器模板。 ** 此步骤可能需要一段时间，具体取决于群集配置。
    
    然后验证以下事项：
    - 门户中的“Service Fabric”部分指示群集已准备就绪。
    - 群集正常运行。
    - 未将属于该节点类型的任何节点标记为种子节点。

4. 禁用节点类型中的每个节点。

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

    - 对于铜级持续性，请等待所有节点进入已禁用状态
    - 对于银级和金级持续性，某些节点将进入已禁用状态，剩余节点将处于正在禁用状态。 检查处于正在禁用状态的节点的详细信息选项卡，如果这些节点全部停滞在确保基础结构服务分区的仲裁状态，则可以安全地继续操作。

5. 停止该节点类型的数据。

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
    
    等待该节点类型的所有节点被标记为“已关闭”。

6. 解除分配原始虚拟机规模集中的节点
    
    登录到在其中部署了规模集的 Azure 订阅，删除虚拟机规模集。 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. 删除该节点类型的数据。

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

    等到所有节点被从群集中删除。 节点应当不再显示在 SFX 中。

8. 从“Service Fabric”部分中删除节点类型。

    - 找到用于部署的 Azure 资源管理器模板。
    - 在“Service Fabric”部分中找到与该节点类型相关的部分。
    - 删除与节点类型对应的部分。
    - （仅适用于银级和更高级别持续性的群集）更新模板中的群集资源，并如下所示通过在群集资源 `properties` 下添加 `applicationDeltaHealthPolicies`，将运行状况策略配置为忽略 fabric:/System 应用程序运行状况。 以下策略应忽略现有错误，但不允许新的运行状况错误。 
 
 
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

    - 部署修改后的 Azure 资源管理器模板。 ** 此步骤需要花费一段时间，通常最长为两个小时。 此项升级会将设置更改为 InfrastructureService，因此需要重启节点。 在这种情况下，将会忽略 `forceRestart`。 
    参数 `upgradeReplicaSetCheckTimeout` 指定 Service Fabric 等待分区进入安全状态（如果尚未进入安全状态）的最长时间。 一旦节点上的所有分区都已通过安全检查，Service Fabric 就会在该节点上继续升级。
    可将参数 `upgradeTimeout` 的值减至 6 小时，但若要获得最高安全性，应使用 12 小时。

    然后验证以下事项：
    - 门户中的 Service Fabric 资源显示准备就绪。

9. 从 ARM 模板中删除对与节点类型相关的资源的所有引用。

    - 找到用于部署的 Azure 资源管理器模板。
    - 从模板中删除与节点类型相关的虚拟机规模集及其他资源。
    - 部署更改。

    然后：
    - 等待部署完成。
    
10. 删除与不再使用的节点类型相关的资源。 示例负载均衡器和公共 IP。 

    - 若要删除这些资源，可以使用在步骤 6 中使用的 PowerShell 命令来指定具体的资源类型和 API 版本。 

> [!Note]
> 如果使用相同的负载均衡器，则此步骤是可选的，并可在节点类型之间重用 IP。

## <a name="next-steps"></a>后续步骤
- 了解有关群集[持续性特征](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)的更多信息。
- 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
- 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。
