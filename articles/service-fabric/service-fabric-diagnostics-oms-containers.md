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
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>使用 Log Analytics 监视容器
 
本文介绍如何为群集设置容器监视。 有关详细信息，请参阅[在 Service Fabric 中监视容器](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers)。 若要查看相关分步教程，还可参照[监视 Service Fabric 上的 Windows 容器](service-fabric-tutorial-monitoring-wincontainers.md)。

## <a name="set-up-the-container-monitoring-solution"></a>设置容器监视解决方案

> [!NOTE]
> 需要为群集设置 Log Analytics，并在节点上部署 OMS 代理。 如果不如此操作，则请首先执行[设置 Log Analytics](service-fabric-diagnostics-oms-setup.md) 和[将 OMS 代理添加到群集](service-fabric-diagnostics-oms-agent.md)中的步骤。

1. 在群集中完成 Log Analytics 和 OMS 代理设置后，请部署容器。 待容器部署完毕后，再执行下一步。

2. 在 Azure Marketplace 中搜索“容器监视解决方案”，并单击“监视 + 管理”类别下显示的“容器监视解决方案”资源。

    ![添加容器解决方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 在已为群集创建的同一工作区内创建解决方案。 此更改自动触发代理开始收集容器上的 docker 数据。 约 15 分钟后，会看到解决方案高亮显示，并显示传入日志和统计信息。

## <a name="next-steps"></a>后续步骤
* 深入了解 Service Fabric 上容器业务流程 - [Service Fabric 和容器](service-fabric-containers-overview.md)
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* 配置 Log Analytics，设置[自动警报](../log-analytics/log-analytics-alerts.md)规则，以辅助检测和诊断