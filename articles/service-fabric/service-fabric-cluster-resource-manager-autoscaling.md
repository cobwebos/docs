---
title: Azure Service Fabric 自动缩放服务和容器
description: 通过 Azure Service Fabric，可设置服务和容器的自动缩放策略。
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3d81feaede7658de69e255c32d3a3ef570156f93
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793085"
---
# <a name="introduction-to-auto-scaling"></a>自动缩放简介
自动扩展是 Service Fabric 的附加功能，可根据服务正在报告的负载或基于资源的使用情况来动态扩展服务。 自动缩放提供了很大的弹性，并可实现按需配置服务的其他实例或分区。 整个自动缩放过程是自动且透明的，一旦在服务上设置策略，就无需在服务级别进行手动缩放操作。 可在创建服务时启用自动缩放，也可在任何时候通过更新服务启用。

特定服务上的负载随时间变化是自动缩放非常实用的一个常见场景。 例如，网关等服务可根据处理传入请求所需的资源量进行缩放。 我们来看看以下示例，了解这些缩放规则：
* 如果我的网关的所有实例平均使用两个以上的核心，则通过再添加一个实例来扩大网关服务。 每小时添加一个，但总共不要超过 7 个实例。
* 如果我的网关的所有实例平均使用少于 0.5 个的核心，则通过删除一个实例来缩小服务。 每小时删除一个，但总共不要少于 3 个实例。

容器和常规 Service Fabric 服务都支持自动缩放。 若要使用自动缩放，需要在 Service Fabric 运行时 6.2 或更高版本上运行。 

本文的其余部分将介绍缩放策略、启用或禁用自动缩放的方式，并举例说明如何使用此功能。

## <a name="describing-auto-scaling"></a>描述自动缩放
可以为 Service Fabric 群集中的每个服务定义自动扩展策略。 每个缩放策略由两部分组成：
* **缩放触发器**描述了将何时执行服务的缩放。 定期检查触发器中定义的条件，确定是否应缩放服务。

* **缩放机制**描述了在触发时将如何执行缩放。 机制仅适用于满足触发条件的情况。

目前支持的所有触发器都可以实用[逻辑加载指标](service-fabric-cluster-resource-manager-metrics.md)，也可以使用物理指标（如CPU或内存使用率）。 无论采用哪种方式，Service Fabric 都将监视所报告的指标负载，并定期评估触发器以确定是否需要缩放。

目前自动缩放的支持机制有两种。 第一种适用于无状态服务或容器，其中通过添加或删除[实例](service-fabric-concepts-replica-lifecycle.md)来执行自动缩放。 对于有状态服务和无状态服务，还可以通过添加或删除服务的命名[分区](service-fabric-concepts-partitioning.md)来执行自动缩放。

> [!NOTE]
> 目前，每个服务仅支持一个缩放策略，并且每个缩放策略仅支持一个缩放触发器。

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>对采用基于实例的缩放的分区负载触发器求平均值
第一种类型的触发器基于无状态服务分区中实例的负载。 指标负载首先经过平滑处理，获得分区中每个实例的负载，然后将分区的所有实例上的这些值求平均。 有三个因素确定何时缩放服务：

* 负载阈值下限是一个用于确定何时将服务缩小的值   。 如果分区的所有实例的平均负载低于此值，将缩小该服务。
* 负载阈值上限是一个用于确定何时将服务扩大的值   。如果分区的所有实例的平均负载高于此值，将扩大该服务。
* 缩放间隔确定检查触发器的频率  。 一旦检查触发器，如果需要缩放，则将应用该机制。 如果不需要缩放，则不会采取任何操作。 在这两种情况下，缩放间隔再次到期之前，不会再检查触发器。

该触发器只能用于无状态服务（无状态容器或 Service Fabric 服务）。 在服务有多个分区的情况下，将分别为每个分区评估触发器，并且每个分区将独立应用指定的机制。 因此，在这种情况下，根据分区的负载，可能会将服务的某些分区扩大，将某些分区会缩小，同时可能不会缩放某些分区。

此触发器可使用的唯一机制是 PartitionInstanceCountScaleMechanism。 有三个因素确定如何应用此机制：
* 缩放增量确定触发机制时将添加或删除多少个实例  。
* 最大实例计数定义了缩放的上限  。 如果分区的实例数量达到此限制，则无论负载如何，都不会扩大服务。 可以通过指定值 -1 来忽略此限制，在这种情况下，服务将尽可能扩大（限制是群集中可用的节点数）。
* 最小实例计数定义了缩放的下限  。 如果分区的实例数量达到此限制，则无论负载如何，都不会缩小服务。

## <a name="setting-auto-scaling-policy"></a>设置自动缩放策略

### <a name="using-application-manifest"></a>使用应用程序清单
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>使用 C# API
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>使用 Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>对采用基于分区的缩放的服务负载触发器求平均值
第二种触发器基于一个服务所有分区的负载。 指标负载首先经过平滑处理，获得分区中每个副本或实例的负载。 对于有状态服务，分区的负载被认为是主要副本的负载，而对于无状态服务，分区的负载是分区的所有实例的平均负载。 这些值在服务的所有分区中取平均值，并且此值用于触发自动缩放。 与之前的机制相同，有三个因素确定将何时缩放服务：

* 负载阈值下限是一个用于确定何时将服务缩小的值   。 如果服务的所有分区的平均负载低于此值，则将缩小该服务。
* 负载阈值上限是一个用于确定何时将服务扩大的值   。如果服务的所有分区的平均负载高于此值，则将扩大该服务。
* 缩放间隔确定检查触发器的频率  。 一旦检查触发器，如果需要缩放，则将应用该机制。 如果不需要缩放，则不会采取任何操作。 在这两种情况下，缩放间隔再次到期之前，不会再检查触发器。

此触发器既可用于有状态服务，也可用于无状态服务。 此触发器唯一可以使用的机制是 AddRemoveIncrementalNamedPartitionScalingMechanism。 扩大服务时，则添加新的分区；缩小服务时，则删除一个现有分区。 在创建或更新服务时会检查一些限制，如果不满足以下条件，则服务创建/更新将失败：
* 命名分区方案必须用于服务。
* 分区名称必须是连续的整数数值，如“0”、“1”、...
* 第一个分区名称必须是“0”。

例如，如果服务最初由三个分区创建，则唯一可能有效的分区名称为“0”、“1”和“2”。

实际执行的自动缩放操作也将遵守此命名方案：
* 如果将服务的当前分区命名为“0”、“1”和“2”，那么将添加用于扩大的分区名为“3”。
* 如果将服务的当前分区命名为“0”、“1”和“2”，那么将删除用于缩小的分区名为“2”。

与通过添加或删除实例使用缩放的机制相同，有三个参数确定如何应用此机制：
* 缩放增量确定触发机制时将添加或删除多少个分区  。
* 最大分区计数定义了缩放的上限  。 如果服务的分区数量达到此限制，则无论负载如何，都不会扩大服务。 可以通过指定值 -1 来忽略此限制，在这种情况下，服务将尽可能扩大（限制是群集的实际容量）。
* 最小实例计数定义了缩放的下限  。 如果服务的分区数量达到此限制，则无论负载如何，都不会缩小服务。

> [!WARNING] 
> 当 AddRemoveIncrementalNamedPartitionScalingMechanism 与有状态服务一起使用时，Service Fabric 将添加或删除分区，**而不会发出通知或警告**。 触发缩放机制时，不会执行数据的重新分区。 对于 scale out 操作，新分区将为空，并且在操作中，**分区将连同它包含的所有数据一起被删除**。

## <a name="setting-auto-scaling-policy"></a>设置自动缩放策略

### <a name="using-application-manifest"></a>使用应用程序清单
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>使用 C# API
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>使用 Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>基于资源的自动缩放

为了使资源监视器服务根据实际资源进行缩放

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
有两个指标表示实际的物理资源。 一个是 servicefabric:/_CpuCores，表示实际 CPU 使用情况（0.5 表示半个核心），另一个是 servicefabric:/_MemoryInMB，表示内存使用情况（以 MB 为单位）。
ResourceMonitorService 负责跟踪用户服务的 CPU 和内存使用情况。 这项服务将应用加权移动平均来解释潜在的短暂高峰。 Windows 上的容器化和非容器化应用程序，以及 Linux 上的容器化应用程序支持资源监视。 仅为[独占进程模型](service-fabric-hosting-model.md#exclusive-process-model)中激活的服务启用资源自动缩放。

## <a name="next-steps"></a>后续步骤
了解有关[应用程序可伸缩性](service-fabric-concepts-scalability.md)的详细信息。
