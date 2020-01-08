---
title: Azure Service Fabric 事件列表
description: 由 Azure Service Fabric 提供的事件综合列表，用于帮助监视群集。
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451761"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 事件列表 

Service Fabric 公开一组主要的群集事件，以通知群集的状态为 [Service Fabric 事件](service-fabric-diagnostics-events.md)。 这些事件基于 Service Fabric 在节点上执行的操作和群集所有者/操作员所做的群集或管理决策。 可以通过配置多个方法来访问这些事件，包括[使用群集配置 Azure Monitor 日志](service-fabric-diagnostics-oms-setup.md)，或查询[EventStore](service-fabric-diagnostics-eventstore.md)。 在 Windows 计算机上，这些事件被传输到 EventLog - 因此可以在“事件查看器”中查看 Service Fabric 事件。 

下面是这些事件的一些特征
* 每个事件被绑定到群集中的特定实体，例如应用程序、服务、节点、副本。
* 每个事件都包含一组公共字段： EventInstanceId、事件名称和类别。
* 每个事件都包含一些字段，这些字段将事件绑定回与之关联的实体。 例如，ApplicationCreated 事件将具有标识创建的应用程序名称的字段。
* 事件以此方式构建，因此，可以在各种工具中使用它们来执行进一步分析。 此外，事件的相关详细信息被定义为单独的属性，而不是一个长字符串。 
* 事件由 Service Fabric 中的不同子系统编写，由下面的源（任务）标识。 有关这些子系统的详细信息，请参阅 [Service Fabric 体系结构](service-fabric-architecture.md)和 [Service Fabric 技术概览](service-fabric-technical-overview.md)。

下面是这些按实体组织的 Service Fabric 事件的列表。

## <a name="cluster-events"></a>群集事件

**群集升级事件**

有关群集升级的详细信息，请参阅[此处](service-fabric-cluster-upgrade-windows-server.md)。

| EventId | 名称 | 类别 | Description |源（任务） | 级别 | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | 升级 | 群集升级已启动 | CM | 信息 |
| 29628 | ClusterUpgradeCompleted | 升级 | 群集升级已完成 | CM | 信息 | 
| 29629 | ClusterUpgradeRollbackStarted | 升级 | 群集升级已开始回退  | CM | 警告 | 
| 29630 | ClusterUpgradeRollbackCompleted | 升级 | 群集升级已完成回退 | CM | 警告 | 
| 29631 | ClusterUpgradeDomainCompleted | 升级 | 升级域在群集升级期间已完成升级 | CM | 信息 | 

## <a name="node-events"></a>节点事件

**节点生命周期事件** 

| EventId | 名称 | 类别 | Description |源（任务） | 级别 |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | 节点停用已完成 | FM | 信息 | 
| 18603 | NodeUp | StateTransition | 群集检测到节点已启动 | FM | 信息 | 
| 18604 | NodeDown | StateTransition | 群集检测到节点已关闭。 在节点重新启动时，将看到后跟 NodeUp 事件的 NodeDown 事件 |  FM | 错误 | 
| 18605 | NodeAddedToCluster | StateTransition |  一个新节点已添加到群集，Service Fabric 可以将应用程序部署到此节点 | FM | 信息 | 
| 18606 | NodeRemovedFromCluster | StateTransition |  从群集中删除了一个节点。 Service Fabric 将无法再将应用程序部署到此节点 | FM | 信息 | 
| 18607 | NodeDeactivateStarted | StateTransition |  节点停用已启动 | FM | 信息 | 
| 25621 | NodeOpenSucceeded | StateTransition |  节点已成功启动 | FabricNode | 信息 | 
| 25622 | NodeOpenFailed | StateTransition |  节点未能启动并加入环 | FabricNode | 错误 | 
| 25624 | NodeClosed | StateTransition |  节点已成功关闭 | FabricNode | 信息 | 
| 25626 | NodeAborted | StateTransition |  一个节点已经以非正常方式关闭 | FabricNode | 错误 | 

## <a name="application-events"></a>应用程序事件

**应用程序生命周期事件**

| EventId | 名称 | 类别 | Description |源（任务） | 级别 | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | 新应用程序已创建 | CM | 信息 | 
| 29625 | ApplicationDeleted | LifeCycle | 一个现有的应用程序已删除 | CM | 信息 | 
| 23083 | ApplicationProcessExited | LifeCycle | 应用程序中的一个进程已退出 | 托管 | 信息 | 

**应用程序升级事件**

有关应用程序升级的详细信息，请参阅[此处](service-fabric-application-upgrade.md)。

| EventId | 名称 | 类别 | Description |源（任务） | 级别 | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | 升级 | 应用程序升级已启动 | CM | 信息 | 
| 29622 | ApplicationUpgradeCompleted | 升级 | 应用程序升级已完成 | CM | 信息 | 
| 29623 | ApplicationUpgradeRollbackStarted | 升级 | 应用程序升级已开始回退 |CM | 警告 | 
| 29624 | ApplicationUpgradeRollbackCompleted | 升级 | 应用程序升级已完成回退 | CM | 警告 | 
| 29626 | ApplicationUpgradeDomainCompleted | 升级 | 升级域在应用程序升级期间已完成升级 | CM | 信息 | 

## <a name="service-events"></a>服务事件

**服务生命周期事件**

| EventId | 名称 | 类别 | Description |源（任务） | 级别 | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | 已创建新服务 | FM | 信息 | 
| 18658 | ServiceDeleted | LifeCycle | 已删除一个现有的服务 | FM | 信息 | 

## <a name="partition-events"></a>分区事件

**分区移动事件**

| EventId | 名称 | 类别 | Description |源（任务） | 级别 | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | 分区重新配置已完成 | RA | 信息 | 

## <a name="replica-events"></a>副本事件

**副本生命周期事件**

| EventId | 名称 | 类别 | Description |源（任务） | 级别 |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | 可靠字典已打开 | DistributedDictionary | 信息 |
| 61702 | ReliableDictionaryClosed | LifeCycle | 可靠字典已关闭 | DistributedDictionary | 信息 |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | 可靠字典已恢复其检查点 | DistributedDictionary | 信息 |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | 副本已发送可靠字典的检查点文件 | DistributedDictionary | 信息 |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | 副本已收到可靠字典的检查点文件 | DistributedDictionary | 信息 |
| 61963 | ReliableQueueOpened | LifeCycle | 可靠队列已打开 | DistributedQueue | 信息 |
| 61964 | ReliableQueueClosed | LifeCycle | 可靠队列已关闭 | DistributedQueue | 信息 |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | 可靠队列已恢复其检查点 | DistributedQueue | 信息 |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | 副本已发送可靠队列的检查点文件 | DistributedQueue | 信息 |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | 副本已收到可靠队列的检查点文件 | DistributedQueue | 信息 |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | 可靠并发队列已打开 | ReliableConcurrentQueue | 信息 |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | 可靠并发队列已关闭 | ReliableConcurrentQueue | 信息 |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | 可靠并发队列已恢复其检查点 | ReliableConcurrentQueue | 信息 |
| 61687 | TStoreError | 失败 | 可靠集合收到意外错误 | TStore | 错误 |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | 主副本已启动完整复制 | TReplicator | 信息 |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | 主副本已启动部分副本 | TReplicator | 信息 |
| 16831 | BuildIdleReplicaStarted | LifeCycle | 主副本已开始生成空闲副本 | 复制 | 信息 |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | 主副本已完成生成空闲副本 | 复制 | 信息 |
| 16833 | BuildIdleReplicaFailed | LifeCycle | 主副本未能生成空闲副本 | 复制 | 警告 |
| 16834 | PrimaryReplicationQueueFull | 医疗业 | 主副本的复制队列已满 | 复制 | 警告 |
| 16835 | PrimaryReplicationQueueWarning | 医疗业 | 主副本的复制队列即将满 | 复制 | 警告 |
| 16836 | PrimaryReplicationQueueWarningMitigated | 医疗业 | 主副本的复制队列正常 | 复制 | 信息 |
| 16837 | SecondaryReplicationQueueFull | 医疗业 | 辅助副本的复制队列已满 | 复制 | 警告 |
| 16838 | SecondaryReplicationQueueWarning | 医疗业 | 辅助副本的复制队列即将满 | 复制 | 警告 |
| 16839 | SecondaryReplicationQueueWarningMitigated | 医疗业 | 辅助副本的复制队列正常 | 复制 | 信息 |
| 16840 | PrimaryFaultedSlowSecondary | 医疗业 | 主副本出错了慢速辅助副本 | 复制 | 警告 |
| 16841 | ReplicatorFaulted | 医疗业 | 副本出错 | 复制 | 警告 |

## <a name="container-events"></a>容器事件

**容器生命周期事件** 

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | 容器已启动 | 托管 | 信息 | 第 |
| 23075 | ContainerDeactivated | 容器已停止 | 托管 | 信息 | 第 |
| 23082 | ContainerExited | 容器已退出 - 请检查 UnexpectedTermination 标志 | 托管 | 信息 | 第 |

## <a name="health-reports"></a>运行状况报告

[Service Fabric 运行状况模型](service-fabric-health-introduction.md)提供丰富、灵活且可扩展的运行状况评估和报告。 从 Service Fabric 版本 6.2 开始，运行状况数据将作为平台事件写入，以提供运行状况历史记录。 为保持较低的运行状况事件数量，我们只将以下内容作为 Service Fabric 事件编写：

* 所有 `Error` 或 `Warning` 运行状况报告
* 转换期间的 `Ok` 运行状况报告
* 当 `Error` 或 `Warning` 运行状况事件过期。 这可以用于确定一个实体不正常的时间

**群集运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | 新的群集运行状况报告可用 | HM | 信息 | 第 |
| 54437 | ClusterHealthReportExpired | 现有的群集运行状况报告已过期 | HM | 信息 | 第 |

**节点运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | 新的节点运行状况报告可用 | HM | 信息 | 第 |
| 54432 | NodeHealthReportExpired | 现有的节点运行状况报告已过期 | HM | 信息 | 第 |

**应用程序运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | 新的应用程序运行状况报告已创建。 这适用于未部署的应用程序。 | HM | 信息 | 第 |
| 54426 | DeployedApplicationNewHealthReport | 新的已部署应用程序运行状况报告已创建 | HM | 信息 | 第 |
| 54427 | DeployedServicePackageNewHealthReport | 新的已部署服务运行状况报告已创建 | HM | 信息 | 第 |
| 54434 | ApplicationHealthReportExpired | 现有的应用程序运行状况报告已过期 | HM | 信息 | 第 |
| 54435 | DeployedApplicationHealthReportExpired | 现有的已部署应用程序运行状况报告已过期 | HM | 信息 | 第 |
| 54436 | DeployedServicePackageHealthReportExpired | 现有的已部署服务运行状况报告已过期 | HM | 信息 | 第 |

**服务运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | 新的服务运行状况报告已创建 | HM | 信息 | 第 |
| 54433 | ServiceHealthReportExpired | 现有的服务运行状况报告已过期 | HM | 信息 | 第 |

**分区运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | 新的分区运行状况报告已创建 | HM | 信息 | 第 |
| 54431 | PartitionHealthReportExpired | 现有的分区运行状况报告已过期 | HM | 信息 | 第 |

**副本运行状况报告事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | 有状态副本运行状况报告已创建 | HM | 信息 | 第 |
| 54430 | StatelessInstanceNewHealthReport | 新的无状态实例运行状况报告已创建 | HM | 信息 | 第 |
| 54438 | StatefulReplicaHealthReportExpired | 现有的有状态副本运行状况报告已过期 | HM | 信息 | 第 |
| 54439 | StatelessInstanceHealthReportExpired | 现有的无状态实例运行状况报告已过期 | HM | 信息 | 第 |

## <a name="chaos-testing-events"></a>混沌测试事件 

**混沌会话事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | 混沌测试会话已启动 | Testability | 信息 | 第 |
| 50023 | ChaosStopped | 混沌测试会话已停止 | Testability | 信息 | 第 |

**混沌节点事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | 节点已计划在混沌测试会话过程中重启 | Testability | 信息 | 第 |
| 50087 | ChaosNodeRestartCompleted | 节点已在混沌测试会话过程中完成重启 | Testability | 信息 | 第 |

**混沌应用程序事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | 已计划在混沌测试会话过程中进行代码包重启 | Testability | 信息 | 第 |
| 50101 | ChaosCodePackageRestartCompleted | 代码包重启已在混沌测试会话过程中完成 | Testability | 信息 | 第 |

**混沌分区事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | 主分区已计划在混沌测试会话过程中移动 | Testability | 信息 | 第 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | 辅助分区已计划在混沌测试会话过程中移动 | Testability | 信息 | 第 |
| 65003 | PartitionPrimaryMoveAnalysis | 可以对主分区移动进行更深入的分析 | Testability | 信息 | 第 |

**混沌副本事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | 已计划在混沌测试会话过程中进行副本重启 | Testability | 信息 | 第 |
| 50051 | ChaosReplicaRemovalScheduled | 已计划在混沌测试会话过程中进行副本删除 | Testability | 信息 | 第 |
| 50093 | ChaosReplicaRemovalCompleted | 已完成在混沌测试会话过程中进行副本删除的操作 | Testability | 信息 | 第 |

## <a name="other-events"></a>其他事件

**相关事件**

| EventId | 名称 | Description |源（任务） | 级别 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 已检测到相关性 | Testability | 信息 | 第 |

## <a name="events-prior-to-version-62"></a>6\.2 版本之前的事件

以下是 6.2 版本之前的 Service Fabric 提供的事件综合列表。

| EventId | 名称 | 源（任务） | 级别 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 信息 |
| 25621 | NodeOpenedSuccess | FabricNode | 信息 |
| 25622 | NodeOpenedFailed | FabricNode | 信息 |
| 25623 | NodeClosing | FabricNode | 信息 |
| 25624 | NodeClosed | FabricNode | 信息 |
| 25625 | NodeAborting | FabricNode | 信息 |
| 25626 | NodeAborted | FabricNode | 信息 |
| 29627 | ClusterUpgradeStart | CM | 信息 |
| 29628 | ClusterUpgradeComplete | CM | 信息 |
| 29629 | ClusterUpgradeRollback | CM | 信息 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 信息 |
| 29631 | ClusterUpgradeDomainComplete | CM | 信息 |
| 23074 | ContainerActivated | 托管 | 信息 |
| 23075 | ContainerDeactivated | 托管 | 信息 |
| 29620 | ApplicationCreated | CM | 信息 |
| 29621 | ApplicationUpgradeStart | CM | 信息 |
| 29622 | ApplicationUpgradeComplete | CM | 信息 |
| 29623 | ApplicationUpgradeRollback | CM | 信息 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 信息 |
| 29625 | ApplicationDeleted | CM | 信息 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 信息 |
| 18566 | ServiceCreated | FM | 信息 |
| 18567 | ServiceDeleted | FM | 信息 |

## <a name="next-steps"></a>后续步骤

* 大致了解 [Service Fabric 中的诊断](service-fabric-diagnostics-overview.md)
* 有关 EventStore 的详细信息，请参阅 [Service Fabric Eventstore 概述](service-fabric-diagnostics-eventstore.md)
* 将 [Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)配置修改为收集更多日志
* [设置 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 以查看操作通道日志
