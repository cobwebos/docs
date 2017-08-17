---
title: "Azure Service Fabric 操作通道 | Microsoft Docs"
description: "完整列出了在 Azure Service Fabric 群集的操作通道中生成的日志。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---

# <a name="operational-channel"></a>操作通道 

操作通道包含 Service Fabric 对节点和群集执行的高级操作生成的日志。 为群集启用“诊断”后，Azure 诊断代理便在群集上进行部署，并默认配置为读取操作通道中的日志。 详细了解如何配置 [Azure 诊断代理](service-fabric-diagnostics-event-aggregation-wad.md)，以便修改群集的诊断配置，从而获取更多日志或性能计数器。 

## <a name="operational-channel-logs"></a>操作通道日志 

下面完整列出了 Service Fabric 在操作通道中提供的日志。 

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

