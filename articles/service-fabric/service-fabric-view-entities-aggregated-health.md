<properties
   pageTitle="如何查看 Azure Service Fabric 实体的聚合运行状况 | Microsoft Azure"
   description="说明如何通过运行状况查询和常规查询来查询、查看和评估 Azure Service Fabric 实体聚合运行状况。"
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/26/2016"
   wacn.date=""/>

# 查看 Service Fabric 运行状况报告
Azure Service Fabric 引入了一种由运行状况实体组成的[运行状况模型](/documentation/articles/service-fabric-health-introduction)，系统组件和监视器可以在其上报告它们监视的本地状况。[运行状况存储](/documentation/articles/service-fabric-health-introduction#health-store)聚合所有运行状况数据以确定实体是否正常运行。

根据现有设定，群集会被系统组件发送的运行状况报告填充。从[使用系统运行状况报告进行故障排除](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)了解更多信息。

Service Fabric 提供多种方式来获取实体聚合运行状况：

- [Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)或其他可视化工具

- 运行状况查询（通过 PowerShell、API 或 REST）

- 常规查询，返回将运行状况作为属性之一的实体的列表（通过 PowerShell、API 或 REST）

为了演示这些选项，让我们使用一个具有五个节点的本地群集。**fabric:/System** 应用程序（原本即已存在）旁边，已部署其他一些应用程序。其中之一是 **fabric:/WordCount**。该应用程序包含一个配置有七个副本的有状态服务。由于只有五个节点，因此系统组件显示分区低于目标计数的警告。

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Service Fabric 资源管理器中的运行状况
Service Fabric 资源管理器提供群集的更直观展示。在下图中，你可以看到：

- 应用程序 **fabric:/WordCount** 为红色（出错），因为 **MyWatchdog** 报告“可用性”属性有一个错误事件。

- 其服务之一，**fabric:/WordCount/WordCount.Service** 为黄色（警告）。如上所述，服务配置有七个副本，这些副本无法全部放置（因为只有五个节点）。尽管此处未显示，因为系统报告，服务分区为黄色。黄色分区触发黄色服务。

- 由于应用程序为红色，因此群集为红色。

评估使用群集清单和应用程序清单的默认策略。它们是严格的策略，不容许任何失败。

使用 Service Fabric 资源管理器查看群集：

![使用 Service Fabric 资源管理器查看群集。][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] 了解有关 [Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)的更多信息。

## 运行状况查询
Service Fabric 为每个支持的[实体类型](/documentation/articles/service-fabric-health-introduction#health-entities-and-hierarchy)提供运行状况查询。可以通过 API（可在 **FabricClient.HealthManager** 中找到的方法）、PowerShell cmdlet 和 REST 访问它们。这些查询返回有关实体的完整运行状况信息，包括聚合运行状况、报告的实体上的运行状况事件、子运行状况（在适用时）以及实体不正常时的不正常评估。

> [AZURE.NOTE] 完全填充运行状况存储中时，运行状况实体返回给用户。实体必须是作用中（未删除），并且具有系统报告。层次结构链上其父实体还必须有系统报告。如果不满足上述条件之一，则运行状况查询返回一个异常，并显示未返回实体的原因。

运行状况查询必须传递取决于实体类型的实体标识符。这些查询接受可选的运行状况策略参数。如果未指定这些参数，则使用来自群集清单或应用程序清单的[运行状况策略](/documentation/articles/service-fabric-health-introduction#health-policies)进行评估。这些查询还接受筛选器，以仅返回与指定筛选器有关的部分子项或事件。

> [AZURE.NOTE] 在服务器端应用输出筛选器，因此减小了消息回复大小。我们建议使用输出筛选器来限制返回的数据，而不是在客户端应用筛选器。

实体运行状况包含以下信息：

- 实体的聚合运行状况状态。这由运行状况存储依据实体运行状况报告、子项运行状况（在适用时）和运行状况策略计算。了解有关[实体运行状况评估](/documentation/articles/service-fabric-health-introduction#entity-health-evaluation)的详细信息。  

- 实体上的运行状况事件。

- 对于能够拥有子项的实体，为所有子项的运行状况集合。运行状况包含实体标识符和聚合的运行状况。若要获取某个子项的完整运行状况，请调用子实体类型的运行状况查询，并传递子实体标识符。

- 如果实体不正常，指向触发实体状态的报告的不正常评估。

## 获取群集运行状况
返回群集实体的运行状况，并包含应用程序和节点（群集的子项）的运行状况。输入：

- [可选] 应用程序运行状况策略与用于取代应用程序清单策略的运行状况策略进行映射。

- [可选] 事件、节点和应用程序的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件、节点及应用程序都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要获取群集运行状况，请创建 **FabricClient** 并在其 **HealthManager** 上调用 [**GetClusterHealthAsync**](https://msdn.microsoft.com/zh-cn/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) 方法。

以下代码获取群集运行状况：

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

以下代码使用针对节点和应用程序的自定义运行状况策略和筛选器获取群集运行状况。注意，它创建包含所有输入数据的 **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/zh-cn/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**。

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

### PowerShell
用于获取群集运行状况的 cmdlet 为 **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/zh-cn/library/mt125850.aspx)**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。

群集的状态：五个节点，系统应用程序和 fabric:/WordCount 按如上配置。

以下 cmdlet 使用默认运行状况策略获取群集运行状况。聚合的运行状况为警告，因为 fabric:/WordCount 应用程序处于警告状态。请注意不正常评估如何提供触发聚合运行状况的详细条件。

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

以下 PowerShell cmdlet 使用自定义应用程序策略获取群集的运行状况。它筛选结果以只获取有错误或警告的应用程序和节点。因此，不会返回任何节点，因为这些节点都是正常的。仅 fabric:/WordCount 应用程序符合应用程序筛选器。因为自定义策略指定对于 fabric:/WordCount 应用程序将警告视为错误，应用程序被评估为错误，从而群集也被评估为错误。

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
返回节点实体的运行状况，并包含针对该节点报告的运行状况事件。输入：

- [必需] 标识该节点的节点名称。

- [可选 ] 用于评估运行状况的群集运行状况策略设置。

- [可选] 事件的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取节点运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetNodeHealthAsync** 方法。

以下代码获取具有指定节点名称的节点运行状况：

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

以下代码获取具有指定节点名称的节点运行状况，通过 **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/zh-cn/library/azure/system.fabric.description.nodehealthquerydescription.aspx)** 传递事件筛选器和自定义策略：

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
用于获取节点运行状况的 cmdlet 为 **Get-ServiceFabricNodeHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。
以下 cmdlet 使用默认运行状况策略获取节点运行状况：

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

以下 cmdlet 获取群集中所有节点的运行状况：

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
返回一个应用程序实体的运行状况。包含已部署应用程序和服务子项的运行状况。输入：

- [必需] 标识应用程序的应用程序名称 (URI)。

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 事件、服务和已部署应用程序的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件、服务和已部署应用程序的都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要获取应用程序运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetApplicationHealthAsync** 方法。

以下代码获取具有指定应用程序名称 (URI) 的应用程序的运行状况：

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

以下代码使用通过 **System.Fabric.Description.ApplicationHealthQueryDescription** 指定的筛选器和自定义策略，获取具有指定应用程序名称 (URI) 的应用程序的运行状况。

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

### PowerShell
用于获取应用程序运行状况的 cmdlet 为 **Get-ServiceFabricApplicationHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。

以下 cmdlet 返回 fabric:/WordCount 应用程序的运行状况：

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

以下 PowerShell cmdlet 传入自定义策略。它还筛选子项和事件。

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
返回一个服务实体的运行状况。包含分区运行状况。输入：

- [必需] 标识服务的服务名称 (URI)。
- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。
- [可选] 事件和分区的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件和分区都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取服务运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetServiceHealthAsync** 方法。

以下示例获取具有指定服务名称 (URI) 的服务的运行状况：

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

以下代码通过 System.Fabric.Description.ServiceHealthQueryDescription 指定筛选器和自定义策略，获取具有指定服务名称 (URI) 的服务的运行状况。

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
用于获取服务运行状况的 cmdlet 为 **Get-ServiceFabricServiceHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。

以下 cmdlet 使用默认运行状况策略获取服务运行状况：

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
返回一个分区实体的运行状况。包含副本运行状况。输入：

- [必需] 标识分区的分区 ID (GUID)。

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 事件和副本的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件和副本都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取分区运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetPartitionHealthAsync** 方法。若要指定可选参数，请创建 **System.Fabric.Description.PartitionHealthQueryDescription**。

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
用于获取分区运行状况的 cmdlet 为 **Get-ServiceFabricPartitionHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。

以下 cmdlet 获取字数统计服务的所有分区的运行状况：

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

- [必需] 分区 ID (GUID) 和标识副本的副本 ID。

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略参数。

- [可选] 事件的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取副本运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetReplicaHealthAsync** 方法。若要指定高级参数，请使用 **System.Fabric.Description.ReplicaHealthQueryDescription。**

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
用于指定副本运行状况的 cmdlet 为 **Get-ServiceFabricReplicaHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。

以下 cmdlet 获取服务所有分区的主副本的运行状况：

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
返回部署在节点实体上的一个应用程序的运行状况。包含已部署服务包的运行状况。输入：

- [必需] 标识已部署应用程序的应用程序名称 (URI) 和节点名称（字符串）。

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 事件和已部署服务包的筛选，指定对那些条目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件和已部署服务包的都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取部署在节点上的一个应用程序的运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetDeployedApplicationHealthAsync** 方法。若要指定可选参数，请使用 **System.Fabric.Description.DeployedApplicationHealthQueryDescription**。

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
用于获取已部署应用程序的运行状况的 cmdlet 为 **Get-ServiceFabricDeployedApplicationHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。若要找出应用程序部署在哪里，运行 **Get-ServiceFabricApplicationHealth** 并查看已部署应用程序子项。

以下 cmdlet 获取部署在 Node.1 上的 fabric:/WordCount 应用程序的运行状况。

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

- [必需] 标识已部署服务包的应用程序名称 (URI)、节点名称（字符串）和服务清单名称（字符串）。

- [可选] 用于取代应用程序清单策略的应用程序运行状况策略。

- [可选] 事件的筛选，指定对那些项目感到兴趣，并且应该在结果中返回（例如，仅限错误、或警告和错误）。请注意，所有事件都用于评估实体聚合运行状况，无论筛选器为何。

### API
若要通过 API 获取一个已部署服务包的运行状况，请创建 FabricClient 并在其 HealthManager 上调用 **GetDeployedServicePackageHealthAsync** 方法。

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
用于获取已部署服务包的运行状况的 cmdlet 是 **Get-ServiceFabricDeployedServicePackageHealth**。首先使用 **Connect-ServiceFabricCluster** cmdlet 连接到群集。若要了解应用程序部署在哪里，运行 **Get-ServiceFabricApplicationHealth** 并查看已部署的应用程序。若要查看一个应用程序中有哪些服务包，请在 **Get-ServiceFabricDeployedApplicationHealth** 输出中查看已部署服务包子项。

以下 cmdlet 获取 Node.1 上的 fabric:/WordCount 应用程序的 **WordCount.Service** 服务包的运行状况。实体具有服务包和入口点 **System.Hosting** 报告以及服务类型成功注册报告。

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
常规查询返回指定类型的 Service Fabric 实体的列表。可以通过 API（通过 **FabricClient.QueryManager** 上的方法）、PowerShell cmdlet 和 REST 使用它们。这些查询聚合了来自多个组件的子查询。其中一个组件是[运行状况存储](/documentation/articles/service-fabric-health-introduction#health-store)，该组件为每个查询结果填充聚合的运行状况状态。

> [AZURE.NOTE] 一般查询返回实体的聚合运行状况状态，不包含丰富的运行状况数据。如果一个实体不正常，你可以通过运行状况查询来跟进，获得所有运行状况信息，包括事件、子项运行状况状态和不正常评估。

如果一般查询返回实体的未知运行状况，则可能表示运行状况存储中不存在有关该实体的完整数据。此外，也有可能对运行状况存储的子查询未成功（例如，发生通信错误，或运行状况存储已被限制）。通过对实体进行运行状况查询进行跟进。如果子查询发生暂时性错误，例如网络问题，此跟进查询可能成功。它还可以从运行状况存储提供关于为何实体未公开的详细信息。

包含实体的 **HealthState** 的查询为：

- 节点列表：返回群集中节点的列表。
  - API：FabricClient.QueryManager.GetNodeListAsync
  - PowerShell：Get-ServiceFabricNode
- 应用程序列表：返回群集中应用程序的列表。
  - API：FabricClient.QueryManager.GetApplicationListAsync
  - PowerShell：Get-ServiceFabricApplication
- 服务列表：返回应用程序中服务的列表。
  - API：FabricClient.QueryManager.GetServiceListAsync
  - PowerShell：Get-ServiceFabricService
- 分区列表：返回服务中分区的列表。
  - API：FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell：Get-ServiceFabricPartition
- 副本列表：返回分区中副本的列表。
  - API：FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell：Get-ServiceFabricReplica
- 已部署应用程序列表：返回节点上已部署应用程序的列表。
  - API：FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell：Get-ServiceFabricDeployedApplication
- 已部署服务包列表：返回已部署应用程序中服务包的列表。
  - API：FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell：Get-ServiceFabricDeployedApplication

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

以下 cmdlet 获取运行状况状态为警告的服务：

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
在群集与应用程序的受监视升级期间，Service Fabric 将检查运行状况，以确保一切都能维持在运行状况良好的状态。如果实体通过使用已设置的运行状况策略评估为状况不良，升级过程将应用升级特定的策略来确定后续措施。升级可能会暂停，以允许用户交互（例如修复错误条件或更改策略），或是它自动回滚到以前的正常版本。

在群集升级期间，你可以获取群集升级状态。这包括任何状况不正常的评估，指向群集中状况不正常的项目。如果升级因运行状况问题而回滚，则升级状态将保留最后的不正常原因。这可以让管理员能够调查发生的问题。

同样地，在应用程序升级期间，应用程序升级状态还包含任何状况不正常的评估。

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

了解有关 [Service Fabric 应用程序升级](/documentation/articles/service-fabric-application-upgrade)的详细信息。

## 使用系统运行状况评估进行故障排除
如果群集或应用程序出现问题，请立即查看群集或应用程序的运行状况以找出错误。不正常评估将提供是什么触发了当前不正常状态的详细信息。如果需要，你可以向下钻取到状况不正常的子实体，以识别根本原因。

## 后续步骤
[使用系统运行状况报告进行故障排除](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)

[添加自定义 Service Fabric 运行状况报告](/documentation/articles/service-fabric-report-health)

[在本地监视和诊断服务](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

[Service Fabric 应用程序升级](/documentation/articles/service-fabric-application-upgrade)

<!---HONumber=Mooncake_0321_2016-->