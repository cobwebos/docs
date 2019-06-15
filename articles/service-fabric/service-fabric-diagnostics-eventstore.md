---
title: Azure Service Fabric EventStore | Microsoft Docs
description: 了解 Azure Service Fabric 的 EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: f1e7428bc0665cdd3f981bb9c2e7b1f564598f40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074238"
---
# <a name="eventstore-overview"></a>EventStore 概述

>[!NOTE]
>从 Service Fabric 版本 6.4 开始。 EventStore API 仅可用于在 Azure 上运行的 Windows 群集。 我们正在将此功能移植到 Linux 以及我们的独立群集。

## <a name="overview"></a>概述

EventStore 服务是从版本 6.2 中引入的，它是 Service Fabric 中的监视选项。 EventStore 提供了在给定时间点中了解群集或工作负载的状态的方法。
EventStore 是有状态 Service Fabric 服务，它维护群集中的事件。 事件通过 Service Fabric Explorer、REST 和 API 公开。 EventStore 直接查询群集来获取关于群集中的任何实体的诊断数据，并且应当用来帮助执行以下操作：

* 在开发或测试时或者当可能使用监视管道时对问题进行诊断
* 确认正在正确处理对群集执行的管理操作
* 获取 Service Fabric 如何与特定实体进行交互的“快照”

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

若要查看 EventStore 中可用的事件的完整列表，请参阅 [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)。

>[!NOTE]
>从 Service Fabric 版本 6.4 开始。 EventStore Api 和用户体验已公开上市的 Azure Windows 群集。 我们正在将此功能移植到 Linux 以及我们的独立群集。

可以向 EventStore 服务查询可用于群集中的每个实体和实体类型的事件。 这意味着可以在以下级别查询事件：
* 群集：特定于群集本身的事件（例如群集升级）
* 多节点：所有节点级事件
* 节点：特定于一个节点的事件，由 `nodeName` 标识
* 多应用程序：所有应用程序级事件
* 应用程序：特定于一个应用程序的事件，由 `applicationId` 标识
* 多服务：来自群集中所有服务的事件
* 服务：来自特定服务的事件，由 `serviceId` 标识
* 多分区：来自所有分区的事件
* 分区：来自特定分区的事件，由 `partitionId` 标识
* 分区副本：来自所有副本的事件/特定分区中的实例，由 `partitionId` 标识
* 分区副本：来自特定副本的事件/实例，由 `replicaId` 和 `partitionId` 标识

若要了解有关 API 的详细信息，请查看 [EventStore API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)。

EventStore 服务还能够将群集中的事件相关联。 通过查看在同一时间从可能已相互影响的不同实体写入的事件，EventStore 服务能够将这些事件进行关联来帮助查明群集中发生各项活动的原因。 例如，如果某个应用程序变得不正常且没有诱发任何变化，则 EventStore 将查看由平台公开的其他事件并且可能会将此情况与 `Error` 或 `Warning` 事件相关联。 这有助于更快地进行故障检测和根本原因分析。

## <a name="enable-eventstore-on-your-cluster"></a>在群集上启用 EventStore

### <a name="local-cluster"></a>本地群集

在[群集中的 fabricSettings.json](service-fabric-cluster-fabric-settings.md) 中，添加 EventStoreService 作为 addOn 功能，并执行群集升级。

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure 群集版本 6.5 +
如果你的 Azure 群集获取升级到版本 6.5 或更高版本中，将在群集上自动启用 EventStore。 若要选择退出，需要使用以下内容更新群集模板：

* 使用的 API 版本`2019-03-01`或更高版本 
* 将以下代码添加到您在群集中的属性部分
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure 群集版本 6.4

如果使用的版本 6.4，可以编辑您的 Azure 资源管理器模板，若要打开 EventStore 服务。 这是通过执行[群集配置升级](service-fabric-cluster-config-upgrade-azure.md)并添加以下代码，可以使用 PlacementConstraints EventStore 服务的副本置于特定 NodeType 例如节点类型专用于系统服务. `upgradeDescription` 部分配置配置升级，以触发节点上的重新启动。 可以在其他更新中删除该部分。

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>后续步骤
* 开始使用 EventStore API - [在 Azure Service Fabric 群集中使用 EventStore API](service-fabric-diagnostics-eventstore-query.md)
* 详细了解有关 EventStore 提供的事件的列表 - [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)
* Service Fabric 中的监视和诊断概述 - [Service Fabric 的监视和诊断](service-fabric-diagnostics-overview.md)
* 查看 API 调用的完整列表 - [EventStore REST API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* 了解有关监视群集的详细信息 - [监视群集和平台](service-fabric-diagnostics-event-generation-infra.md)。
