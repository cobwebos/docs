---
title: "使用系统运行状况报告进行故障排除 | Microsoft 文档"
description: "介绍了 Azure Service Fabric 组件发送的运行状况报告，以及如何使用这些报告来排查群集或应用程序问题"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: b02b1260cedcade9bf69a99453ab0f5aa2c3c7b1
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="use-system-health-reports-to-troubleshoot"></a>使用系统运行状况报告进行故障排除
Azure Service Fabric 组件提供有关现成群集中所有实体的系统运行状况报告。 [运行状况存储](service-fabric-health-introduction.md#health-store)根据系统报告来创建和删除实体。 它还将这些实体组织为层次结构以捕获实体交互。

> [!NOTE]
> 请阅读 [Service Fabric 运行状况模型](service-fabric-health-introduction.md)以了解与运行状况相关的概念。
> 
> 

使用系统运行状况报告，不仅可以查看群集和应用程序功能，还能标记问题。 对于应用程序和服务，系统运行状况报告从 Service Fabric 的角度验证实体得到实现并且正常运行。 报告既不监视服务的业务逻辑运行状况，也不检测挂起的进程。 用户服务可以使用其逻辑的特有信息来丰富运行状况数据。

> [!NOTE]
> 用户监视程序发送的运行状况报告仅在系统组件创建实体后才可见。 如果实体遭到删除，运行状况存储会自动删除与实体相关联的所有运行状况报告。 如果新建的是实体的新实例，也同样如此；例如，当新建有状态持久化服务副本实例时。 所有与旧实例关联的报告都将从存储中删除并清除。
> 
> 

按来源标识系统组件报告，并以“**System**”。 前缀开头。 监视器不能与来源使用相同的前缀，因为如果参数无效，报告会被拒绝。

接下来，将以一些系统报告为例，介绍是什么触发生成这些报告，以及如何纠正报告指出的潜在问题。

> [!NOTE]
> Service Fabric 会继续添加报告，让用户更清楚地了解群集中的情况，并且会在应用程序现有报告中添加更多详细信息，有助于用户更快地排查问题。
> 
> 

## <a name="cluster-system-health-reports"></a>群集系统运行状况报告
群集运行状况实体在运行状况存储中自动创建。 如果一切工作正常，则不会有任何系统报告。

### <a name="neighborhood-loss"></a>邻居丢失
**System.Federation** 在检测到邻居丢失时会报告一个错误。 报告来自于单个节点，并且在属性名称中包含节点 ID。 如果整个 Service Fabric 环缺少一个邻近区域，通常可以有两个事件，分别代表间隙报告的两端。 如果有多个邻居丢失，则将有更多事件。

报告将全局租用超时指定为生存时间 (TTL)。 只要条件仍处于活动状态，就会在每半个 TTL 期间重新发送一次报告。 事件到期后会自动将其删除。 过期后删除行为可以确保从运行状况存储中正常清理报告，即使在报告节点停止运行时，也不例外。

* **SourceId**：System.Federation
* **属性**：以 Neighborhood 开头，包含节点信息。
* **后续步骤**：调查邻近区域丢失原因；例如，检查群集节点之间的通信。

## <a name="node-system-health-reports"></a>节点系统运行状况报告
**System.FM** 表示故障转移管理器 (Failover Manager) 服务，是管理群集节点信息的主管服务。 每个节点应该都有一个来自 System.FM 的报告，显示其状态。 节点实体随节点状态一起删除。 有关详细信息，请参阅 [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync)。

### <a name="node-updown"></a>节点开启/节点关闭
当节点加入环时，System.FM 报告为正常（节点已启动且正在运行）。 当节点离开环时，则报告错误（节点已关闭进行升级，或只是发生故障）。 运行状况存储生成的运行状况层次结构对与 System.FM 节点报告相关的已部署实体起作用。 它将节点视为所有已部署实体的虚拟父项。 如果 System.FM 报告节点处于开启状态，且拥有的实例与在该节点上部署的实体相关联的实例相同，则可以通过查询看到这些实体。 如果 System.FM 报告节点停止运行或重启（作为新实例），运行状况存储会自动清理只能位于停止运行的节点或节点的上一实例上的已部署实体。

* **SourceId**：System.FM
* **属性**：State。
* **后续步骤**：如果节点是因为升级而停止运行，应该会在升级后恢复运行。 在这种情况下，运行状况应切换回正常。 如果节点没有重新启动或发生故障，则需要进一步调查问题。

以下示例显示 System.FM 事件，且节点正常运行时的运行状况状态为正常：

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>证书过期日期
**System.FabricNode** 在节点使用的证书即将过期时报告警告。 每个节点有三个证书：**Certificate_cluster**、**Certificate_server** 和 **Certificate_default_client**。 如果过期时间至少超过两周，报告运行状况是正常。 如果过期时间在两周内，则报告类型是警告。 这些事件的 TTL 是无限的，当节点离开群集时，它们被删除。

* **SourceId**：System.FabricNode
* **属性**：以 Certificate 开头，包含证书类型的详细信息。
* **后续步骤**：如果证书即将过期，则更新证书。

### <a name="load-capacity-violation"></a>负载容量冲突
如果 Service Fabric 负载均衡器检测到节点负载容量冲突，则报告警告。

* **SourceId**：System.PLB
* **属性**：以 Capacity 开头。
* **后续步骤**：检查已提供的指标，并查看节点上的当前容量。

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>资源调控指标的节点容量不匹配
如果在群集清单中定义的节点容量大于资源调控指标（内存和 CPU 内核）的实际节点容量，System.Hosting 将报告一个警告。 首个使用[资源调控](service-fabric-resource-governance.md)的服务包在指定节点上注册时，将显示运行状况报告。

* **SourceId**：System.Hosting
* 属性：ResourceGovernance
* 后续步骤：这可能是一个问题，因为服务包不会按预期进行强制调控并且[资源调控](service-fabric-resource-governance.md)工作不正常。 使用这些指标的正确节点容量更新群集清单，或根本不指定，让 Service Fabric 自动检测可用资源。

## <a name="application-system-health-reports"></a>应用程序系统运行状况报告
**System.CM** 表示群集管理器服务，是管理应用程序信息的主管服务。

### <a name="state"></a>状态
当创建或更新应用程序时，System.CM 报告正常。 它会在应用程序遭到删除时通知运行状况存储，以便将它从存储中删除。

* **SourceId**：System.CM
* **属性**：State。
* **后续步骤**：如果已创建或更新应用程序，它就应该包含群集管理器运行状况报告。 否则，通过发出查询（例如，PowerShell cmdlet Get-ServiceFabricApplication -ApplicationName applicationName），检查应用程序的状态。

以下示例显示 **fabric:/WordCount** 应用程序上的状态事件：

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>服务系统运行状况报告
**System.FM** 表示故障转移管理器服务，是管理服务信息的主管服务。

### <a name="state"></a>状态
当已创建服务时，System.FM 报告正常。 当已删除服务时，它从运行状况存储删除实体。

* **SourceId**：System.FM
* **属性**：State。

以下示例显示服务 **fabric:/WordCount/WordCountWebService** 上的状态事件：

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>服务相关错误
检测到更新服务与形成关联链的其他服务相关时，System.PLB 会报告错误。 更新成功后会清除报告。

* **SourceId**：System.PLB
* **属性**：ServiceDescription。
* **后续步骤**：检查相关服务说明。

## <a name="partition-system-health-reports"></a>分区系统运行状况报告
**System.FM** 表示故障转移管理器服务，是管理服务分区信息的主管服务。

### <a name="state"></a>状态
创建分区并且分区正常时，System.FM 报告正常。 当删除分区时，它从运行状况存储删除实体。

如果分区小于最小副本计数，则它将报告错误。 如果分区不小于最低副本计数，但小于目标副本计数，将会报告警告。 如果分区在仲裁丢失中，则 System.FM 将报告错误。

其他显著事件包括，在重新配置时间长于预期以及生成时间长于预期时发出警告。 生成和重新配置的预期时长可根据服务方案进行配置。 例如，如果服务的状态为 1TB（如 Azure SQL 数据库），那么生成时间就长于状态量小的服务。

* **SourceId**：System.FM
* **属性**：State。
* **后续步骤**：如果健康状况不正常，则有可能某些副本没有正确创建、打开或提升为主副本或次要副本。 

如果说明描述仲裁丢失，请检查并备份已停止运行副本的详细运行状况报告，这有助于让分区重新上线。

如果说明描述分区无法运行[重新配置](service-fabric-concepts-reconfiguration.md)，主要副本的运行状况报告还提供其他信息。

对于其他 System.FM 运行状况报告，还有其他系统组件中副本、分区或服务的相关报告。 

下面的示例展示了其中一些报告。 

以下示例显示了一个运行状况良好的分区：

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

下面的示例展示了小于目标副本计数的分区运行状况。 下一步是获取分区描述，其中为分区配置方式：MinReplicaSetSize 为 3，TargetReplicaSetSize 为 7。 然后，获取群集中的节点数（在此示例中为 5）。 因此，在此示例中，无法放置两个副本，因为副本的目标数量大于可用节点数。

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

下面的示例展示了无法运行重新配置（原因是用户不履行 RunAsync 方法中的取消令牌）的分区运行状况。 调查标记为主要 (P) 的任何副本的运行状况报告有助于深入了解问题。

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
此运行状况报告显示正在执行重新配置的分区的副本状态： 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

对于每个副本，运行状况报告包含：
- 旧配置角色
- 当前配置角色
- [副本状态](service-fabric-concepts-replica-lifecycle.md)
- 运行副本的节点
- 副本 ID

在此示例中，需要进一步调查。 调查上一示例中以标记为 `Primary` 和 `Secondary` 的副本（131482789658160654 和 131482789688598467）开头的各个副本的运行状况。

### <a name="replica-constraint-violation"></a>副本约束冲突
如果 **System.PLB** 检测到副本约束冲突并且无法放置所有分区副本，则报告警告。 报告详细信息会显示哪些约束和属性阻止了副本放置。

* **SourceId**：System.PLB
* **属性**：以 ReplicaConstraintViolation 开头。

## <a name="replica-system-health-reports"></a>副本系统运行状况报告
**System.RA** 表示重新配置代理组件，是用于处理副本状态的主管组件。

### <a name="state"></a>状态
在副本创建后，System.RA 报告正常。

* **SourceId**：System.RA
* **属性**：State。

以下示例显示了一个运行状况良好的副本：

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
此属性用于在用户尝试打开副本、关闭副本或将副本从一个角色转换为另一个角色时，指示警告或故障。 有关详细信息，请参阅[副本生命周期](service-fabric-concepts-replica-lifecycle.md)。 这些故障可能是 API 调用抛出的异常，也可能是在这段时间内服务主机进程发生的故障。 对于因 C# 代码中的 API 调用而发生的故障，Service Fabric 会在运行状况报告中添加异常和堆栈跟踪。

这些运行状况警告是在本地重试操作数次（具体取决于策略）后发出的。 Service Fabric 重试操作的次数不得超过最大阈值。 达到最大阈值后，它可能会尝试采取措施来纠正这种情况。 这样的尝试可能会导致这些警告遭到清除，因为它放弃对此节点执行操作。 例如，如果副本无法在节点上打开，Service Fabric 会发出运行状况警告。 如果副本仍无法打开，Service Fabric 会进行自我修复。 此操作可能会涉及在另一个节点上尝试同一操作。 这就会导致针对此副本发出的警告遭到清除。 

* **SourceId**：System.RA
* **属性**：ReplicaOpenStatus、ReplicaCloseStatus 和 ReplicaChangeRoleStatus。
* 后续步骤：调查服务代码或故障转储，确定操作失败的原因。

下面的示例展示了从打开方法抛出 `TargetInvocationException` 的副本运行状况。 说明包含故障点 (IStatefulServiceReplica.Open)、异常类型 (TargetInvocationException) 和堆栈跟踪。

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

下面的示例展示了在关闭期间不断发生故障的副本：

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>重新配置
此属性用于指示执行[重新配置](service-fabric-concepts-reconfiguration.md)的副本何时检测到重新配置已停止或受阻。 此运行状况报告可能针对的是当前角色为主要的副本，交换主要重新配置的情况除外，在这种情况下，此报告可能针对的是从主要降级为活动次要的副本。

重新配置可能会因以下原因之一而无法运行：

- 本地副本（与执行重新配置相同的副本）上的操作尚未完成。 在这种情况下，从其他组件（System.RAP 或 System.RE）调查此副本的运行状况报告可能会获得其他信息。

- 远程副本上的操作尚未完成。 运行状况报告中列出了操作挂起的副本。 应对这些远程副本的运行状况报告进行进一步调查。 此节点和远程节点之间也可能存在通信问题。

在极少数情况下，重新配置可能会因为此节点和故障转移管理器服务之间的通信问题或其他问题而无法运行。

* **SourceId**：System.RA
* **属性**：Reconfiguration。
* 后续步骤：根据运行状况报告的说明调查本地或远程副本。

下面的示例展示了重新配置在本地副本上无法运行的运行状况报告。 在此示例中，这是由于服务不履行取消令牌所致。

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

下面的示例展示了重新配置因等待两个远程副本的响应而无法运行的运行状况报告。 在此示例中，分区中有三个副本，包括当前的主要副本。 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

此运行状况报告显示重新配置因等待两个副本的响应而无法运行： 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

对于每个副本，给出了以下信息：
- 旧配置角色
- 当前配置角色
- [副本状态](service-fabric-concepts-replica-lifecycle.md)
- 节点 ID
- 副本 ID

若要取消阻止重新配置：
- 应启动 down 副本。 
- inbuild 副本应完成生成，并切换到就绪状态。

### <a name="slow-service-api-call"></a>服务 API 调用缓慢
如果对用户服务代码的调用时间超过配置的时间，则 **System.RAP** 和 **System.Replicator** 报告警告。 当调用完成时，警告被清除。

* **SourceId**：System.RAP 或 System.Replicator
* **属性**：慢速 API 的名称。 说明提供了有关 API 挂起时间的详细信息。
* **后续步骤**：调查调用时间超过预期的原因。

下面的示例展示了 System.RAP 中因 Reliable Service 不履行 RunAsync 中的取消令牌而发生的运行状况事件：

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

属性和文本指明了哪些 API 无法运行。 后续步骤因无法运行的 API 是哪个而异。 IStatefulServiceReplica 或 IStatelessServiceInstance 上的任何 API 通常都是服务代码中的 bug。 下面的部分介绍了如何将上述内容转换为 [Reliable Services 模型](service-fabric-reliable-services-lifecycle.md)：

- **IStatefulServiceReplica.Open**：此警告指明无法调用 `CreateServiceInstanceListeners`、`ICommunicationListener.OpenAsync` 或 `OnOpenAsync`（若已重写）。

- **IStatefulServiceReplica.Close** 和 **IStatefulServiceReplica.Abort**：最常见的情况是服务不履行传递给 `RunAsync` 的取消令牌。 也可能是无法调用 `ICommunicationListener.CloseAsync` 或 `OnCloseAsync`（若已重写）。

- **IStatefulServiceReplica.ChangeRole(S)** 和 **IStatefulServiceReplica.ChangeRole(N)**：最常见的情况是服务不履行传递给 `RunAsync` 的取消令牌。

- **IStatefulServiceReplica.ChangeRole(P)**：最常见的情况是服务没有从 `RunAsync` 返回任务。

可能会在 IReplicator 接口上无法调用其他 API。 例如：

- **IReplicator.CatchupReplicaSet**：此警告指明发生以下两个事件之一。 up 副本不足，可通过查看分区中副本的副本状态或指明无法运行重新配置的 System.FM 运行状况报告来确定。 或副本不确认操作。 PowerShell command-let `Get-ServiceFabricDeployedReplicaDetail` 可用于确定所有副本的进度。 问题在于，某些副本的 `LastAppliedReplicationSequenceNumber` 落后于主要副本的 `CommittedSequenceNumber`。

- **IReplicator.BuildReplica (<Remote ReplicaId>)**：此警告指明生成过程有问题。 有关详细信息，请参阅[副本生命周期](service-fabric-concepts-replica-lifecycle.md)。 这可能是由于复制器地址配置错误所致。 有关详细信息，请参阅[配置有状态可靠服务](service-fabric-reliable-services-configuration.md)和[在服务清单中指定资源](service-fabric-service-manifest-resources.md)。 也可能是远程节点有问题。

### <a name="replication-queue-full"></a>复制队列已满
如果复制队列已满，则 **System.Replicator** 报告警告。 在主要副本上，由于一个或多个次要副本确认操作的速度较慢，复制队列通常会达到已满状态。 在辅助副本上，当服务应用操作的速度较慢时，通常会发生这种情况。 当队列不再满时，警告被清除。

* **SourceId**：System.Replicator
* **属性**：PrimaryReplicationQueueStatus 或 SecondaryReplicationQueueStatus（视副本角色而定）。

### <a name="slow-naming-operations"></a>命名操作速度慢
如果命名操作耗时超过可接受范围，System.NamingService 会报告主要副本的运行状况。 [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) 或 [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) 都是命名操作的示例。 在 FabricClient 下可找到更多方法，例如，可在[服务管理方法](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)或[属性管理方法](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)下找到更多方法。

> [!NOTE]
> 命名服务将服务名称解析到群集中的某个位置，并允许用户管理服务名称和属性。 它是 Service Fabric 分区持久化服务。 其中一个分区代表“颁发机构所有者”，内含与所有 Service Fabric 名称和服务相关的元数据。 Service Fabric 名称映射到不同的分区，这些分区称为“名称所有者”分区，因此服务是可扩展的。 有关详细信息，请参阅[命名服务](service-fabric-architecture.md)。
> 
> 

如果命名操作耗时超出预期，则会在为操作提供服务的命名服务分区的主要副本上使用警告报告对操作进行标记。 如果操作成功完成，将会清除警告。 如果操作在完成时出现错误，则运行状况报告中会包括有关该错误的详细信息。

* **SourceId**：System.NamingService
* **属性**：以前缀“Duration_”开头，用于发现速度慢的操作以及其上应用了操作的 Service Fabric 名称。 例如，如果名称 fabric:/MyApp/MyService 处的创建服务操作耗时过长，则属性为 Duration_AOCreateService.fabric:/MyApp/MyService。 “AO”指向此名称和操作的命名分区角色。
* **后续步骤**：查看命名操作失败的原因。 每个操作可能会有不同的根本原因。 例如，可能无法删除服务。 服务可能会无法运行，因为应用程序主机总是在节点上发生故障，原因是服务代码中存在用户 bug。

以下示例显示了创建服务操作。 该操作花的时间超过配置的持续时间。 “AO”重试并将工作发送到“NO”。 “NO”在完成上一个操作时出现超时。 在这种情况下，同一个副本对于“AO”和“NO”角色来说都是主要副本。

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication 系统运行状况报告
**System.Hosting** 是已部署实体的主管组件。

### <a name="activation"></a>激活
当应用程序在节点上成功激活时，System.Hosting 报告正常。 否则报告错误。

* **SourceId**：System.Hosting
* **属性**：Activation，包括滚动更新版本。
* **后续步骤**：如果应用程序不正常，则调查激活失败的原因。

下面的示例展示了成功激活：

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>下载
如果应用程序包下载失败，System.Hosting 会报告错误。

* **SourceId**：System.Hosting
* **属性**：Download:RolloutVersion。
* **后续步骤**：调查在节点上下载失败的原因。

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage 系统运行状况报告
**System.Hosting** 是已部署实体的主管组件。

### <a name="service-package-activation"></a>服务包激活
如果服务包在节点上成功激活，则 System.Hosting 报告正常。 否则报告错误。

* **SourceId**：System.Hosting
* **属性**：Activation。
* **后续步骤**：调查激活失败的原因。

### <a name="code-package-activation"></a>代码包激活
对于每个代码包，如果成功激活，System.Hosting 报告正常。 如果激活失败，则报告配置的警告。 如果 **CodePackage** 无法激活，或者由于错误数超过配置的 **CodePackageHealthErrorThreshold** 而终止，则 Hosting 报告错误。 如果服务包中有多个代码包，则为每个包生成激活报告。

* **SourceId**：System.Hosting
* **属性**：使用前缀 CodePackageActivation，并包含 CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint 形式的代码包名称和入口点。 例如，CodePackageActivation:Code:SetupEntryPoint。

### <a name="service-type-registration"></a>服务类型注册
如果服务类型注册成功，System.Hosting 报告正常。 如果注册未按时完成（超时是通过 ServiceTypeRegistrationTimeout 配置），则报告错误。 如果运行时已关闭，服务类型会从节点取消注册，并且 Hosting 会报告警告。

* **SourceId**：System.Hosting
* **属性**：使用前缀 ServiceTypeRegistration，并包含服务类型名称。 例如，ServiceTypeRegistration:FileStoreServiceType。

以下示例显示了一个正常的已部署服务包：

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>下载
如果服务包下载失败，System.Hosting 报告错误。

* **SourceId**：System.Hosting
* **属性**：Download:RolloutVersion。
* **后续步骤**：调查在节点上下载失败的原因。

### <a name="upgrade-validation"></a>升级验证
如果升级期间验证失败或节点上的升级失败，System.Hosting 报告错误。

* **SourceId**：System.Hosting
* **属性**：使用前缀 FabricUpgradeValidation，并包含升级版本。
* **说明**：指向遇到的错误。

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>资源调控指标的节点容量未定义
如果未在群集清单中定义节点容量，且自动检测被配置为关闭，则 System.Hosting 将报告一个警告。 只要使用[资源调控](service-fabric-resource-governance.md)的服务包在指定节点上注册，Service Fabric 就会引发一个运行状况警报。

* **SourceId**：System.Hosting
* 属性：ResourceGovernance
* 后续步骤：要解决此问题，首选方法是更改群集清单以启用可用资源的自动检测功能。 另一种方法是使用为这些指标正确指定的节点容量来更新群集清单。

## <a name="next-steps"></a>后续步骤
[查看 Service Fabric 运行状况报告](service-fabric-view-entities-aggregated-health.md)

[如何报告和检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[在本地监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 应用程序升级](service-fabric-application-upgrade.md)

