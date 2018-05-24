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
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 事件列表 

Service Fabric 公开一组主要的群集事件，以通知群集的状态为 [Service Fabric 事件](service-fabric-diagnostics-events.md)。 这些事件基于 Service Fabric 在节点上执行的操作和群集所有者/操作员所做的群集或管理决策。 可以通过在群集中查询 [EventStore](service-fabric-diagnostics-eventstore.md) 或通过操作通道来访问这些事件。 在 Windows 计算机上，操作通道还与 EventLog 相连接 - 因此可以在“事件查看器”中查看 Service Fabric 事件。 

>[!NOTE]
>对于 6.2 以下版本中的群集的 Service Fabric 事件列表，请参阅以下部分。 

以下是平台中所有可用事件的列表，按事件映射到的实体排序。

## <a name="cluster-events"></a>群集事件

**群集升级事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | 信息性 | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | 信息性 | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | 信息性 | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | 信息性 | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | 信息性 | 1 |

**群集运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | 信息性 | 1 |
| 54437 | ExpiredClusterEventOperational | HM | 信息性 | 1 |

**混沌服务事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Testability | 信息性 | 1 |
| 50023 | ChaosStoppedEvent | Testability | 信息性 | 1 |

## <a name="node-events"></a>节点事件

**节点生命周期事件** 

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | 信息性 | 1 |
| 18603 | NodeUpOperational | FM | 信息性 | 1 |
| 18604 | NodeDownOperational | FM | 信息性 | 1 |
| 18605 | NodeAddedOperational | FM | 信息性 | 1 |
| 18606 | NodeRemovedOperational | FM | 信息性 | 1 |
| 18607 | DeactivateNodeStartOperational | FM | 信息性 | 1 |
| 25620 | NodeOpening | FabricNode | 信息性 | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | 信息性 | 1 |
| 25622 | NodeOpenedFailed | FabricNode | 信息性 | 1 |
| 25623 | NodeClosing | FabricNode | 信息性 | 1 |
| 25624 | NodeClosed | FabricNode | 信息性 | 1 |
| 25625 | NodeAborting | FabricNode | 信息性 | 1 |
| 25626 | NodeAborted | FabricNode | 信息性 | 1 |

**节点运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | 信息性 | 1 |
| 54432 | ExpiredNodeEventOperational | HM | 信息性 | 1 |

**混沌节点事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Testability | 信息性 | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Testability | 信息性 | 1 |

## <a name="application-events"></a>应用程序事件

**应用程序生命周期事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | 信息性 | 1 |
| 29625 | ApplicationDeletedOperational | CM | 信息性 | 1 |
| 23083 | ProcessExitedOperational | Hosting | 信息性 | 1 |

**应用程序升级事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | 信息性 | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | 信息性 | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | 信息性 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | 信息性 | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | 信息性 | 1 |

**应用程序运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | 信息性 | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | 信息性 | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | 信息性 | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | 信息性 | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | 信息性 | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | 信息性 | 1 |

**混沌应用程序事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Testability | 信息性 | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Testability | 信息性 | 1 |

## <a name="service-events"></a>服务事件

**服务生命周期事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | 信息性 | 1 |
| 18658 | ServiceDeletedOperational | FM | 信息性 | 1 |

**服务运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | 信息性 | 1 |
| 54433 | ExpiredServiceEventOperational | HM | 信息性 | 1 |

## <a name="partition-events"></a>分区事件

**分区移动事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | 信息性 | 1 |

**分区运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | 信息性 | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | 信息性 | 1 |

**混沌分区事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Testability | 信息性 | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Testability | 信息性 | 1 |

**分区分析事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Testability | 信息性 | 1 |

## <a name="replica-events"></a>副本事件

**副本运行状况报告事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | 信息性 | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | 信息性 | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | 信息性 | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | 信息性 | 1 |

**混沌副本事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Testability | 信息性 | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Testability | 信息性 | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Testability | 信息性 | 1 |

## <a name="container-events"></a>容器事件

**容器生命周期事件** 

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | 信息性 | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | 信息性 | 1 |
| 23082 | ContainerExitedOperational | Hosting | 信息性 | 1 |

## <a name="other-events"></a>其他事件

**相关事件**

| EventId | 名称 | 源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Testability | 信息性 | 1 |

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
