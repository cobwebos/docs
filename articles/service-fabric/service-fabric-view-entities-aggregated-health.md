<properties
   pageTitle="如何查看 Azure Service Fabric 实体聚合运行状况"
   description="说明如何通过运行状况查询和常规查询来查询、查看和评估 Azure Service Fabric 实体聚合运行状况。"
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 如何查看 Service Fabric 运行状况报告
Service Fabric 引入了一种由运行状况实体组成的[运行状况模型](/documentation/articles/service-fabric-health-introduction)，系统组件和监视器可以在其上报告它们监视的本地状况。[运行状况存储](/documentation/articles/service-fabric-health-introduction#health-store)聚合所有运行状况数据以确定实体是否正常运行。

根据现有设定，群集会填充系统组件发送的运行状况报告。了解有关[使用系统运行状况报告进行故障排除](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)的更多信息。

Service Fabric 提供多种方式来获取实体聚合运行状况：

- [Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)或其他可视化工具

- 运行状况查询（通过 Powerhsell/API/REST）

- 常规查询，返回将运行状况作为属性之一的实体的列表（通过 Powershell/API/REST）

为了演示这些选项，让我们使用一个具有 **5 个节点**的本地群集。在 **fabric:/System** 应用程序（已经存在）旁边，已经部署有其他应用程序，其中一个为 **fabric:/WordCount**。该应用程序包含一个配置有 7 个副本的有状态服务。因为只有 5 个节点，系统组件将标记分区少于目标计数并发出警告。

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Service Fabric 资源管理器中的运行状况
Service Fabric 资源管理器提供群集的更直观展示。在以下图片中，你可以看到：

- 应用程序 **fabric:/WordCount** 为“红色”（错误），因为 MyWatchdog 报告可用性属性有一个错误事件。

- 其服务之一，**fabric:/WordCount/WordCount.Service** 为“黄色”（警告）。如上所述，服务配置有 7 个副本，这些副本无法全部放置（因为只有 5 个节点）。尽管此处未显示，因为系统报告，服务分区为“黄色”。“黄色”分区触发“黄色”服务。

- 由于应用程序为“红色”，所以**群集**为“红色”。

评估使用来自群集清单和应用程序清单的默认策略，这些策略为严格策略（不容忍任何失败）。

![使用 ServiceFabricExplorer 查看群集。][1]

使用 ServiceFabricExplorer 查看群集。

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]了解有关 [Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)的更多信息。

## 运行状况查询
Service Fabric 为每个支持的[实体类型](/documentation/articles/service-fabric-health-introduction#health-entities-and-hierarchy)提供运行状况查询。可以通过 API（FabricClient.HealthManager 上的方法）、Powershell cmdlet 和 REST 访问它们。这些查询返回有关实体的完整运行状况信息，包括聚合运行状况状态、报告的实体上的运行状况事件、子运行状况状态（在适用时）以及实体不正常时的不正常评估。

> [AZURE.NOTE]当一个运行状况实体完全填入运行状况存储中时会返回到用户：即实体有一个系统报告，它是活动的（未被删除）且在层次结构链中的父实体有系统报告。如果不满足上述条件之一，则运行状况查询返回一个异常，并显示未返回实体的原因。

运行状况查询需要传递取决于实体类型的实体标识符。它们接受可选的运行状况策略参数。如果未指定，则使用来自群集清单或应用程序清单的[运行状况策略](/documentation/articles/service-fabric-health-introduction#health-policies)进行评估。它们还接受筛选器，以仅返回与指定筛选器有关的部分子项或事件。

> [AZURE.NOTE]在服务器端应用输出筛选器，因此减小了消息回复大小。建议使用筛选器来限制返回的数据，而不是在客户端应用筛选器。

实体运行状况包含以下信息：

- 实体的聚合运行状况状态。这由运行状况存储依据实体运行状况报告、子项运行状况状态（在适用时）和运行状况策略计算。了解有关[实体运行状况评估](/documentation/articles/service-fabric-health-introduction#entity-health-evaluation)的更多信息。  

- 实体上的运行状况事件。

- 对于能够拥有子项的实体，为所有子项的运行状况状态集合。运行状况状态包含实体标识符和聚合的运行状况状态。若要获取某个子项的完整运行状况，请调用子实体类型的运行状况查询，并传递子实体标识符。

- 如果实体不正常，指向触发实体状态的报告的不正常评估。

## 获取群集运行状况
返回群集实体的运行状况。包含应用程序和节点（群集的子项）的运行状况状态。输入：

- [可选] 应用程序运行状况策略与用于取代应用程序清单策略的运行状况策略进行映射。

- [可选] 用于只返回具有某种运行状况状态的事件、节点、应用程序的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要获取群集运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetClusterHealthAsync 方法。

以下代码获取群集运行状况：

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

以下代码使用针对节点和应用程序的自定义运行状况策略和筛选器获取群集运行状况。注意，它创建包含所有输入数据的 System.Fabric.Description.ClusterHealthQueryDescription。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### Powershell
用于获取群集运行状况的 cmdlet 为 Get-ServiceFabricClusterHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。群集的状态：5 个节点，系统应用程序和 fabric:/WordCount 按如上配置。

以下 cmdlet 使用默认运行状况策略获取群集运行状况。聚合的运行状况状态为警告，因为 fabric:/WordCount 应用程序处于警告状态。请注意不正常评估如何显示触发聚合运行状况的详细条件。

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

以下 Powershell cmdlet 使用自定义应用程序策略获取群集的运行状况。它筛选结果以只获取有错误或警告的应用程序和节点。因此，不会返回任何节点，因为这些节点都是正常的。仅 fabric:/WordCount 应用程序符合应用程序筛选器。因为自定义策略指定对于 fabric:/WordCount 应用程序将警告视为错误，应用程序被评估为错误，从而群集也被评估为错误。

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## 获取节点运行状况
返回一个节点实体的运行状况。包含所报告节点上的运行状况事件。输入：

- [必需] 标识该节点的节点名称。

- [可选 ]用于评估运行状况的群集运行状况策略设置。

- [可选] 用于只返回具有某种运行状况状态的事件的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取节点运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetNodeHealthAsync 方法

以下代码获取具有指定节点名称的节点运行状况。

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

以下代码获取具有指定节点名称的节点运行状况，通过 System.Fabric.Description.NodeHealthQueryDescription 传递事件筛选器和自定义策略。

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### Powershell
用于获取节点运行状况的 cmdlet 为 Get-ServiceFabricNodeHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。以下 cmdlet 使用默认运行状况策略获取节点运行状况。

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

以下 cmdlet 获取群集中所有节点的运行状况。

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## 获取应用程序运行状况
返回一个应用程序实体的运行状况。包含已部署应用程序和服务子项的运行状况状态。输入：

- [必需] 标识应用程序的应用程序名称 (Uri)

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 用于只返回具有某种运行状况状态的事件、服务、已部署应用程序的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要获取应用程序运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetApplicationHealthAsync 方法。

以下代码获取具有指定应用程序名称 Uri 的应用程序的运行状况。

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

以下代码获取具有指定应用程序名称 Uri 的应用程序的运行状况，通过 System.Fabric.Description.ApplicationHealthQueryDescription 指定筛选器和自定义策略。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### Powershell
用于获取应用程序运行状况的 cmdlet 为 Get-ServiceFabricApplicationHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。

以下 cmdlet 返回 fabric:/WordCount 应用程序的运行状况。

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

以下 Powershell 传递自定义策略并筛选子项和事件。

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## 获取服务运行状况
返回一个服务实体的运行状况。包含分区运行状况状态。输入：

- [必需] 标识服务的服务名称 (Uri)
- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。
- [可选] 用于只返回具有某种运行状况状态的事件和分区的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取服务运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetServiceHealthAsync 方法

以下示例获取具有指定服务名称 (Uri) 的服务的运行状况：

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

以下代码获取具有指定服务名称 Uri 的服务的运行状况，通过 System.Fabric.Description.ServiceHealthQueryDescription 指定筛选器和自定义策略。

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### Powershell
用于获取服务运行状况的 cmdlet 为 Get-ServiceFabricServiceHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。

以下 cmdlet 使用默认运行状况策略获取服务运行状况。

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## 获取分区运行状况
返回一个分区实体的运行状况。包含副本运行状况状态。输入：

- [必需] 标识分区的分区 ID (Guid)

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 用于只返回具有某种运行状况状态的事件、副本的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取分区运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetPartitionHealthAsync 方法若要指定可选参数，请创建 System.Fabric.Description.PartitionHealthQueryDescription。

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### Powershell
用于获取分区运行状况的 cmdlet 为 Get-ServiceFabricPartitionHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。

以下 cmdlet 获取字数统计服务的所有分区的运行状况。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## 获取副本运行状况
返回一个副本的运行状况。输入：

- [必需] 分区 ID (Guid) 和标识副本的副本 ID

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略参数。

- [可选] 用于只返回具有某种运行状况状态的事件的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取副本运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetReplicaHealthAsync 方法使用 System.Fabric.Description.ReplicaHealthQueryDescription 指定高级参数。

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### Powershell
用于指定副本运行状况的 cmdlet 为 Get-ServiceFabricReplicaHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。

以下 cmdlet 获取服务所有分区的主副本的运行状况。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 获取已部署应用程序的运行状况
返回部署在节点实体上的一个应用程序的运行状况。包含已部署服务包的运行状况状态。输入：

- [必需] 标识已部署应用程序的应用程序名称 (Uri) 和节点名称（字符串）

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 用于只返回具有某种运行状况状态的事件、已部署服务包的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取部署在节点上的一个应用程序的运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetDeployedApplicationHealthAsync 方法。若要指定可选参数，请使用 System.Fabric.Description.DeployedApplicationHealthQueryDescription。

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### Powershell
用于获取已部署应用程序的运行状况的 cmdlet 为 Get-ServiceFabricDeployedApplicationHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。若要找出应用程序部署在哪里，运行 Get-ServiceFabricApplicationHealth 并查看已部署应用程序子项。

以下 cmdlter 获取部署在节点 Node.1 上的 fabric:/WordCount 应用程序的运行状况。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 获取已部署服务包的运行状况
返回一个已部署服务包实体的运行状况。输入：

- [必需] 标识已部署服务包的应用程序名称 (Uri)、节点名称（字符串）和服务清单名称（字符串）

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 用于只返回具有某种运行状况状态的事件的筛选器（例如只返回错误或返回警告或错误等）。

### API
若要通过 API 获取一个已部署服务包的运行状况，请创建 FabricClient 并在其 HealthManager 上调用 GetDeployedServicePackageHealthAsync 方法。

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### Powershell
用于获取已部署服务包的运行状况的 cmdlet 是 Get ServiceFabricDeployedServicePackageHealth。首先使用 Connect-ServiceFabricCluster cmdlet 连接到群集。若要查看应用程序部署在哪里，运行 Get-ServiceFabricApplicationHealth 并查看已部署应用程序。若要查看一个应用程序中有哪些服务包，请在 Get-ServiceFabricDeployedApplicationHealth 输出中查看已部署服务包子项。

以下 cmdlet 获取节点 Node.1 上的 fabric:/WordCount 应用程序的 WordCount.Service 服务包的运行状况。实体具有服务包和入口点成功激活报告以及服务类型成功注册报告。

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 常规查询
常规查询返回指定类型的 Service Fabric 实体的列表。可以通过 API（FabricClient.QueryManager 上的方法）、Powershell cmdlet 和 REST 使用它们。这些查询聚合了来自多个组件的子查询。其中一个组件是[运行状况存储](/documentation/articles/service-fabric-health-introduction#health-store)，该组件为每个查询结果填充聚合的运行状况状态。

> [AZURE.NOTE]一般查询返回实体的聚合运行状况状态，不包含丰富的运行状况数据。如果一个实体不正常，你可以通过运行状况查询来跟进，获得所有运行状况信息，例如事件、子项运行状况状态和不正常评估。

如果常规查询返回一个实体的未知运行状况状态，则可能运行状况存储没有该实体的完整数据，或者对运行状况存储的子查询不成功（例如通信错误、运行状况存储被阻止等）。通过对实体进行运行状况查询进行跟进。如果子查询遇到暂时性错误（例如网络问题），则这可能成功，或者提供有关实体没有出现在运行状况存储中原因的更多信息。

包含实体的运行状况状态的查询为：

- 节点列表。返回群集中节点的列表。
  - API：FabricClient.QueryManager.GetNodeListAsync。
  - Powershell：Get-ServiceFabricNode。
- 应用程序列表。返回群集中应用程序的列表。
  - API：FabricClient.QueryManager.GetApplicationListAsync。
  - Powershell：Get-ServiceFabricApplication。
- 服务列表。返回应用程序中服务的列表。
  - API：FabricClient.QueryManager.GetServiceListAsync。
  - Powershell：Get-ServiceFabricService。
- 分区列表。返回服务中分区的列表。
  - API：FabricClient.QueryManager.GetPartitionListAsync。
  - Powershell：Get-ServiceFabricPartition。
- 副本列表。返回分区中副本的列表。
  - API：FabricClient.QueryManager.GetReplicaListAsync。
  - Powershell：Get-ServiceFabricReplica。
- 已部署应用程序列表。返回节点上已部署应用程序的列表。
  - API：FabricClient.QueryManager.GetDeployedApplicationListAsync。
  - Powershell：Get-ServiceFabricDeployedApplication。
- 已部署服务包列表。返回已部署应用程序中服务包的列表。
  - API：FabricClient.QueryManager.GetDeployedServicePackageListAsync。
  - Powershell：Get-ServiceFabricDeployedApplication。

### 示例

以下代码获取群集中不正常的应用程序：

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

以下 cmdlet 获取 fabric:/WordCount 应用程序的详细信息。请注意，运行状况状态为警告。

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

以下 cmdlet 获取运行状况状态为警告的服务。

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## 群集和应用程序升级
在群集和应用程序有监视升级期间，Service Fabric 检查运行状况以确保所有一切都保持正常。如果某些项目不正常，则依据配置好的策略，升级会暂停以允许用户交互，或者自动回滚。

在**群集**升级期间，你可以获取群集升级状态，包括指出群集中的哪些项目不正常的不正常评估。如果升级因运行状况问题而回滚，则升级状态将保留最后的不正常原因，从而让管理员能够调查发生的问题。类似的，在**应用程序**升级期间，应用程序升级状态包含不正常评估。

以下代码显示修改后的 fabric:/WordCount 应用程序的升级状态。监视程序在其中一个副本上报告一个错误。因为运行状况检查不合格，升级回滚。

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

了解有关 [Service Fabric 应用程序升级](/documentation/articles/service-fabric-application-upgrade)的更多信息。

## 使用运行状况进行故障排除
如果群集或应用程序出现问题，请立即查看群集或应用程序的运行状况以找出错误。不正常评估将显示是什么触发了当前不正常状态的详细信息。如有需要，深入调查不正常子实体以找出问题。

## 后续步骤
[使用系统运行状况报告进行故障排除](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)

[添加自定义 Service Fabric 运行状况报告](/documentation/articles/service-fabric-report-health)

[如何在本地监视和诊断服务](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

[Service Fabric 应用程序升级](/documentation/articles/service-fabric-application-upgrade)
 

<!---HONumber=74-->