---
title: 监视 Azure Service Fabric 上使用 Azure Monitor 日志的容器 |Microsoft Docs
description: 使用 Azure Monitor 日志进行监视 Azure Service Fabric 群集上运行的容器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d5840db718191c9b67a8b28a2efccd55146ae510
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246927"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视容器
 
本文介绍如何设置 Azure Monitor 日志容器监视解决方案，以查看容器事件所需的步骤。 若要将群集设置为收集容器事件，请参阅此[分步教程](service-fabric-tutorial-monitoring-wincontainers.md)。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>设置容器监视解决方案

> [!NOTE]
> 需要具有 Azure Monitor 日志为你的群集设置，以及在节点上部署了 Log Analytics 代理。 如果不这样做，请按照中的步骤[设置 Azure Monitor 日志](service-fabric-diagnostics-oms-setup.md)并[向群集中添加 Log Analytics 代理](service-fabric-diagnostics-oms-agent.md)第一个。

1. 一旦你的群集通过 Azure Monitor 日志和 Log Analytics 代理设置，请部署容器。 待容器部署完毕后，再执行下一步。

2. 在 Azure 市场中搜索“容器监视解决方案”，并单击“监视 + 管理”类别下显示的“容器监视解决方案”资源。

    ![添加容器解决方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 在已为群集创建的同一工作区内创建解决方案。 此更改自动触发代理开始收集容器上的 docker 数据。 约 15 分钟后，应看到解决方案高亮显示，并显示传入日志和统计信息，如下图所示。

    ![基本 Log Analytics 仪表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理启用几个特定于容器的日志可在 Azure Monitor 日志中查询或用于可视化性能指标的收集。 收集的日志类型：

* ContainerInventory：显示有关容器位置、名称和图像的信息
* ContainerImageInventory：有关已部署映像的信息，包括 ID 或大小
* ContainerLog：特定的错误日志、stdout 等 docker 日志和其他条目
* ContainerServiceLog：已运行的 docker 守护程序命令
* Perf：性能计数器，包括容器 cpu、内存、网络流量、磁盘 i/o 和主机的自定义指标



## <a name="next-steps"></a>后续步骤
* 详细了解如何[Azure Monitor 日志的容器解决方案](../azure-monitor/insights/containers.md)。
* 深入了解 Service Fabric 上容器业务流程 - [Service Fabric 和容器](service-fabric-containers-overview.md)
* 掌握[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)作为 Azure Monitor 日志的一部分提供的功能
* 配置 Azure Monitor 日志来设置[自动警报](../log-analytics/log-analytics-alerts.md)规则，以辅助检测和诊断