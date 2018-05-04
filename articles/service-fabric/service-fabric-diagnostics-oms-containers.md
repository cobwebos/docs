---
title: 使用 Log Analytics 监视 Azure Service Fabric 上的容器 | Microsoft Docs
description: 使用 Log Analytics 监视 Azure Service Fabric 群集上运行的容器。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 1de7e58eecc80e306920ab17884290dfddf8efa8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-containers-with-log-analytics"></a>使用 Log Analytics 监视容器
 
本文介绍设置 OMS Log Analytics 容器监视解决方案以查看容器事件所需的步骤。 若要将群集设置为收集容器事件，请参阅此[分步教程](service-fabric-tutorial-monitoring-wincontainers.md)。

## <a name="set-up-the-container-monitoring-solution"></a>设置容器监视解决方案

> [!NOTE]
> 需要为群集设置 Log Analytics，并在节点上部署 OMS 代理。 如果不如此操作，则请首先执行[设置 Log Analytics](service-fabric-diagnostics-oms-setup.md) 和[将 OMS 代理添加到群集](service-fabric-diagnostics-oms-agent.md)中的步骤。

1. 在群集中完成 Log Analytics 和 OMS 代理设置后，请部署容器。 待容器部署完毕后，再执行下一步。

2. 在 Azure Marketplace 中搜索“容器监视解决方案”，并单击“监视 + 管理”类别下显示的“容器监视解决方案”资源。

    ![添加容器解决方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 在已为群集创建的同一工作区内创建解决方案。 此更改自动触发代理开始收集容器上的 docker 数据。 约 15 分钟后，应看到解决方案高亮显示，并显示传入日志和统计信息，如下图所示。

    ![基本 OMS 仪表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理可收集数个特定于容器的日志，可在 OMS 中进行查询或用于直观的性能指示器。 收集的日志类型：

* ContainerInventory：显示有关容器位置、名称和图像的信息
* ContainerImageInventory：有关已部署映像的信息，包括 ID 或大小
* ContainerLog：特定的错误日志、stdout 等 docker 日志和其他条目
* ContainerServiceLog：已运行的 docker 守护程序命令
* Perf：性能计数器，包括容器 cpu、内存、网络流量、磁盘 i/o 和主机的自定义指标



## <a name="next-steps"></a>后续步骤
* 详细了解 [OMS 的容器解决方案](../log-analytics/log-analytics-containers.md)。
* 深入了解 Service Fabric 上容器业务流程 - [Service Fabric 和容器](service-fabric-containers-overview.md)
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* 配置 Log Analytics，设置[自动警报](../log-analytics/log-analytics-alerts.md)规则，以辅助检测和诊断