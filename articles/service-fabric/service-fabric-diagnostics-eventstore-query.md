---
title: 在 Azure Service Fabric 群集中使用 EventStore API 查询群集事件 | Microsoft Docs
description: 了解如何使用 Azure Service Fabric EventStore API 查询平台事件
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 5c184841602f269555ce2196ef660faba14dbf8a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="query-eventstore-apis-for-cluster-events"></a>查询群集事件的 EventStore API

本文介绍如何查询适用于 Service Fabric 版本 6.2 及更高版本的 EventStore API - 如果想要了解有关 EventStore 服务的详细信息，请参阅 [EventStore 服务概述](service-fabric-diagnostics-eventstore.md)。 目前，EventStore 服务仅能访问最近 7 天的数据（这取决于群集的诊断数据保留策略）。

>[!NOTE]
>从 Service Fabric 版本 6.2 开始。 EventStore API 当前为预览版，仅可用于在 Azure 中运行的 Windows 群集。 我们正在将此功能移植到 Linux 以及我们的独立群集。

可以通过 REST 终结点或以编程方式直接访问 EventStore API。 需要几个参数才能收集正确的数据，具体取决于查询。 这些参数通常包括：
* `api-version`：正在使用的 EventStore API 版本
* `StartTimeUtc`：定义要查找的开始时段
* `EndTimeUtc`：结束时段

除此之外，还有一些可选参数，例如：
* `timeout`：替代默认的 60 秒超时，以执行请求操作
* `eventstypesfilter`：支持选择筛选特定事件类型
* `ExcludeAnalysisEvents`：不返回“分析”事件。 默认情况下，EventStore 查询将在可能的情况下返回“分析”事件 - 分析事件是更丰富的操作通道事件，包括超出常规 Service Fabric 事件范围的其他上下文或信息并且更为深入。
* `SkipCorrelationLookup`：不在群集中查找潜在的关联事件。 默认情况下，EventStore 将尝试在群集中关联事件，并在可能的情况下将事件链接到一起。 

可以查询群集中所有实体的事件。 还可以查询特定类型的所有实体的事件。 例如，可以查询特定节点的事件或群集中所有节点的事件。 当前可以查询的事件实体集（以及查询的结构）为：
* 群集：`/EventsStore/Cluster/Events`
* 多个节点：`/EventsStore/Nodes/Events`
* 一个节点：`/EventsStore/Nodes/<NodeName>/$/Events`
* 多个应用程序：`/EventsStore/Applications/Events`
* 一个应用程序：`/EventsStore/Applications/<AppName>/$/Events`
* 多个服务：`/EventsStore/Services/Events`
* 一个服务：`/EventsStore/Services/<ServiceName>/$/Events`
* 多个分区：`/EventsStore/Partitions/Events`
* 一个分区：`/EventsStore/Partitions/<PartitionID>/$/Events`
* 多个副本：`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* 一个副本：`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>引用应用程序或服务名称时，查询不需要包括“fabric:/”前缀。 另外，如果应用程序或服务名称中包含“/”，请将其转换为“~”，以使查询正常工作。 例如，如果应用程序显示为“fabric:/App1/FrontendApp”，则应用特定查询的结构应为 `/EventsStore/Applications/App1~FrontendApp/$/Events`。
>此外，服务的运行状况报告现在在相应的应用程序下方显示，因此需要对正确的应用程序实体查询 `DeployedServiceHealthReportCreated` 事件。 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>通过 REST API 终结点查询 EventStore

将 `GET` 请求设为 `<your cluster address>/EventsStore/<entity>/Events/` 后，可直接通过 REST 终结点查询 EventStore。

例如，若要查询 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之间的所有群集，请求将如下所示：

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

如果没有可用事件，将会返回错误；或者，如果查询成功，则会看到以 json 格式返回的事件：

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

此处可以看到，在 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之间，该群集在首次尝试时成功完成首次升级，从 `"CurrentClusterVersion": "0.0.0.0:"` 升级到 `"TargetClusterVersion": "6.2:1.0"`，所用时间为 `"OverallUpgradeElapsedTimeInMs": "120196.5212"`。

## <a name="query-the-eventstore-programmatically"></a>以编程方式查询 EventStore

还可以通过 [Service Fabric 客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)以编程方式查询 EventStore。

设置好 Service Fabric 客户端后，可以通过访问 EventStore（如 ` sfhttpClient.EventStore.<request>`）来查询事件

以下是通过 `GetClusterEventListAsync` 函数请求 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之间的所有群集事件的示例。

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>示例方案和查询

以下是说明如何通过调用事件存储 REST API 来了解群集状态的几个示例。

群集升级：

若要查看上周群集最后一次成功或尝试升级的情况，可以通过查询 EventStore 中的“ClusterUpgradeComplete”事件来查询 API 以查看群集最近完成的升级：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`

群集升级问题：

同样，如果最新的群集升级出现问题，则可以查询群集实体的所有事件。 将会看到各种事件，包括升级启动和成功完成升级的每个 UD。 还将看到回滚开始时的事件和相应的运行状况事件。 以下查询可用于此种情况：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

节点状态更改：

若要查看过去几天的节点状态更改情况 - 节点上升或下降的时间，或者是处于激活或停用状态（由平台、混沌服务或用户输入导致）的时间 - 请使用以下查询：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

应用程序事件：

还可以跟踪最近的应用程序部署和升级。 使用以下查询，查看与群集中的事件相关的所有应用程序：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

应用程序的运行状况历史记录：

除了仅查看应用程序的生命周期事件，你可能还想查看特定应用程序运行状况的历史记录数据。 可通过指定想收集其数据的应用程序名称来执行此操作。 使用此查询获取所有应用程序历史记录事件：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`。 如果要包括可能已过期的历史记录事件（已过保留时间 (TTL)），请将 `,ExpiredDeployedApplicationEvent` 添加到查询末尾以筛选两种类型的事件。

“myApp”中所有服务的历史记录运行状况：

目前，服务的运行状况报告事件在相应的应用程序实体下方显示为 `DeployedServiceHealthReportCreated` 事件。 若要查看服务如何对“App1”执行操作，请使用以下查询：`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`

分区重新配置：

若要查看在群集中发生的所有分区移动，请查询 `ReconfigurationCompleted` 事件。 这有助于在诊断群集中的问题时，找出在特定时间内哪些工作负载在哪些节点上运行。 这是可执行此操作的示例查询：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`

混沌服务：

当混沌服务开始或停止时，群集级别会公开一个事件。 若要查看最近一次使用混沌服务的情况，请使用以下查询：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

