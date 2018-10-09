---
title: Azure Service Fabric 事件列表 | Microsoft Docs
description: 由 Azure Service Fabric 提供的事件综合列表，用于帮助监视群集。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: da4a779bca806fe6aa392db96eafc6c20f8ddcf6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182155"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 事件列表 

Service Fabric 公开一组主要的群集事件，以通知群集的状态为 [Service Fabric 事件](service-fabric-diagnostics-events.md)。 这些事件基于 Service Fabric 在节点上执行的操作和群集所有者/操作员所做的群集或管理决策。 可以通过在群集中查询 [EventStore](service-fabric-diagnostics-eventstore.md) 或通过操作通道来访问这些事件。 在 Windows 计算机上，操作通道还与 EventLog 相连接 - 因此可以在“事件查看器”中查看 Service Fabric 事件。 

>[!NOTE]
>对于 6.2 以下版本中的群集的 Service Fabric 事件列表，请参阅以下部分。 

以下是平台中所有可用事件的列表，按事件映射到的实体排序。

## <a name="cluster-events"></a>群集事件

**群集升级事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | 群集升级已启动 | CM | 信息性 | 1 |
| 29628 | ClusterUpgradeCompleted | 群集升级已完成| CM | 信息性 | 1 |
| 29629 | ClusterUpgradeRollbackStarted | 群集升级已开始回退 | CM | 信息性 | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | 群集升级已完成回退 | CM | 信息性 | 1 |
| 29631 | ClusterUpgradeDomainCompleted | 域升级已在群集升级过程中完成 | CM | 信息性 | 1 |

## <a name="node-events"></a>节点事件

**节点生命周期事件** 

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | 节点停用已完成 | FM | 信息性 | 1 |
| 18603 | NodeUp | 群集检测到节点已启动 | FM | 信息性 | 1 |
| 18604 | NodeDown | 群集检测到节点已关闭 |  FM | 信息性 | 1 |
| 18605 | NodeAddedToCluster | 新节点已添加到群集 | FM | 信息性 | 1 |
| 18606 | NodeRemovedFromCluster | 从群集中删除了一个节点 | FM | 信息性 | 1 |
| 18607 | NodeDeactivateStarted | 节点停用已启动 | FM | 信息性 | 1 |
| 25620 | NodeOpening | 一个节点正在启动。 节点生命周期的第一阶段 | FabricNode | 信息性 | 1 |
| 25621 | NodeOpenSucceeded | 节点已成功启动 | FabricNode | 信息性 | 1 |
| 25622 | NodeOpenFailed | 节点未能启动 | FabricNode | 信息性 | 1 |
| 25623 | NodeClosing | 节点正在关闭。 节点生命周期的最后阶段开始 | FabricNode | 信息性 | 1 |
| 25624 | NodeClosed | 节点已成功关闭 | FabricNode | 信息性 | 1 |
| 25625 | NodeAborting | 一个节点正在开始以非正常方式关闭 | FabricNode | 信息性 | 1 |
| 25626 | NodeAborted | 一个节点已经以非正常方式关闭 | FabricNode | 信息性 | 1 |

## <a name="application-events"></a>应用程序事件

**应用程序生命周期事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | 新应用程序已创建 | CM | 信息性 | 1 |
| 29625 | ApplicationDeleted | 一个现有的应用程序已删除 | CM | 信息性 | 1 |
| 23083 | ApplicationProcessExited | 应用程序中的一个进程已退出 | Hosting | 信息性 | 1 |

**应用程序升级事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | 应用程序升级已启动 | CM | 信息性 | 1 |
| 29622 | ApplicationUpgradeCompleted | 应用程序升级已完成 | CM | 信息性 | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | 应用程序升级已开始回退 |CM | 信息性 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | 应用程序升级已完成回退 | CM | 信息性 | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | 域升级已在应用程序升级过程中完成 | CM | 信息性 | 1 |

## <a name="service-events"></a>服务事件

**服务生命周期事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | 已创建新服务 | FM | 信息性 | 1 |
| 18658 | ServiceDeleted | 已删除一个现有的服务 | FM | 信息性 | 1 |

## <a name="partition-events"></a>分区事件

**分区移动事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | 分区重新配置已完成 | RA | 信息性 | 1 |

## <a name="container-events"></a>容器事件

**容器生命周期事件** 

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | 容器已启动 | Hosting | 信息性 | 1 |
| 23075 | ContainerDeactivated | 容器已停止 | Hosting | 信息性 | 1 |
| 23082 | ContainerExited | 容器已退出 - 请检查 UnexpectedTermination 标志 | Hosting | 信息性 | 1 |

## <a name="health-reports"></a>运行状况报告

**群集运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | 新的群集运行状况报告可用 | HM | 信息性 | 1 |
| 54437 | ClusterHealthReportExpired | 现有的群集运行状况报告已过期 | HM | 信息性 | 1 |

**节点运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | 新的节点运行状况报告可用 | HM | 信息性 | 1 |
| 54432 | NodeHealthReportExpired | 现有的节点运行状况报告已过期 | HM | 信息性 | 1 |

**应用程序运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | 新的应用程序运行状况报告已创建。 这适用于未部署的应用程序。 | HM | 信息性 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 新的已部署应用程序运行状况报告已创建 | HM | 信息性 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 新的已部署服务运行状况报告已创建 | HM | 信息性 | 1 |
| 54434 | ApplicationHealthReportExpired | 现有的应用程序运行状况报告已过期 | HM | 信息性 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 现有的已部署应用程序运行状况报告已过期 | HM | 信息性 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 现有的已部署服务运行状况报告已过期 | HM | 信息性 | 1 |

**服务运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | 新的服务运行状况报告已创建 | HM | 信息性 | 1 |
| 54433 | ServiceHealthReportExpired | 现有的服务运行状况报告已过期 | HM | 信息性 | 1 |

**分区运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | 新的分区运行状况报告已创建 | HM | 信息性 | 1 |
| 54431 | PartitionHealthReportExpired | 现有的分区运行状况报告已过期 | HM | 信息性 | 1 |

**副本运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | 有状态副本运行状况报告已创建 | HM | 信息性 | 1 |
| 54430 | StatelessInstanceNewHealthReport | 新的无状态实例运行状况报告已创建 | HM | 信息性 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | 现有的有状态副本运行状况报告已过期 | HM | 信息性 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | 现有的无状态实例运行状况报告已过期 | HM | 信息性 | 1 |

## <a name="chaos-testing-events"></a>混沌测试事件 

**混沌会话事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | 混沌测试会话已启动 | Testability | 信息性 | 1 |
| 50023 | ChaosStopped | 混沌测试会话已停止 | Testability | 信息性 | 1 |

**混沌节点事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | 节点已计划在混沌测试会话过程中重启 | Testability | 信息性 | 1 |
| 50087 | ChaosNodeRestartCompleted | 节点已在混沌测试会话过程中完成重启 | Testability | 信息性 | 1 |

**混沌应用程序事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | 已计划在混沌测试会话过程中进行代码包重启 | Testability | 信息性 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | 代码包重启已在混沌测试会话过程中完成 | Testability | 信息性 | 1 |

**混沌分区事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | 主分区已计划在混沌测试会话过程中移动 | Testability | 信息性 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | 辅助分区已计划在混沌测试会话过程中移动 | Testability | 信息性 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | 可以对主分区移动进行更深入的分析 | Testability | 信息性 | 1 |

**混沌副本事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | 已计划在混沌测试会话过程中进行副本重启 | Testability | 信息性 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | 已计划在混沌测试会话过程中进行副本删除 | Testability | 信息性 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | 已完成在混沌测试会话过程中进行副本删除的操作 | Testability | 信息性 | 1 |

## <a name="other-events"></a>其他事件

**相关事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 关联性已分离 | Testability | 信息性 | 1 |

## <a name="events-prior-to-version-62"></a>6.2 版本之前的事件

以下是 6.2 版本之前的 Service Fabric 提供的事件综合列表。

| EventId | 名称 | 源（任务） | 级别 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 信息性 |
| 25621 | NodeOpenedSuccess | FabricNode | 信息性 |
| 25622 | NodeOpenedFailed | FabricNode | 信息性 |
| 25623 | NodeClosing | FabricNode | 信息性 |
| 25624 | NodeClosed | FabricNode | 信息性 |
| 25625 | NodeAborting | FabricNode | 信息性 |
| 25626 | NodeAborted | FabricNode | 信息性 |
| 29627 | ClusterUpgradeStart | CM | 信息性 |
| 29628 | ClusterUpgradeComplete | CM | 信息性 |
| 29629 | ClusterUpgradeRollback | CM | 信息性 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 信息性 |
| 29631 | ClusterUpgradeDomainComplete | CM | 信息性 |
| 23074 | ContainerActivated | Hosting | 信息性 |
| 23075 | ContainerDeactivated | Hosting | 信息性 |
| 29620 | ApplicationCreated | CM | 信息性 |
| 29621 | ApplicationUpgradeStart | CM | 信息性 |
| 29622 | ApplicationUpgradeComplete | CM | 信息性 |
| 29623 | ApplicationUpgradeRollback | CM | 信息性 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 信息性 |
| 29625 | ApplicationDeleted | CM | 信息性 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 信息性 |
| 18566 | ServiceCreated | FM | 信息性 |
| 18567 | ServiceDeleted | FM | 信息性 |

## <a name="next-steps"></a>后续步骤

* 详细了解 Service Fabric 中的[平台级总体事件生成情况](service-fabric-diagnostics-event-generation-infra.md)
* 将 [Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)配置修改为收集更多日志
* [设置 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 以查看操作通道日志
