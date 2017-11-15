---
title: "使用 OMS 进行 Azure Service Fabric 事件分析 | Microsoft Docs"
description: "了解通过使用 OMS 可视化和分析事件来监视和诊断 Azure Service Fabric 群集。"
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 09542c0e7f628ca4fea00a6562c0b9525432c213
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>使用 OMS 进行事件分析和可视化

Operations Management Suite (OMS) 集中了管理服务，有助于监视和诊断云中托管的应用程序的服务。 若要深入了解 OMS 及其功能的详述概况，请参阅[什么是 OMS？](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics 和 OMS 工作区

Log Analytics 从托管资源（包括 Azure 存储表或代理）收集数据，并在中央存储库中维护它们。 之后这些数据可用于分析、报警、可视化或进一步导出。 Log Analytics 支持事件、性能数据或其他任何自定义数据。

配置 OMS 后，将能够访问特定的 OMS 工作区，从此处可在仪表板中查询或可视化数据。

Log Analytics 收到数据后，OMS 提供多个预打包的管理解决方案，用于监视传入数据，且根据不同情形进行自定义。 包括 Service Fabric 分析解决方案和容器解决方案。使用 Service Fabric 群集时，这两种解决方案与诊断和监视最为相关。 还包括其他几个值得探索的解决方案。此外，OMS 允许创建自定义解决方案，可参阅[此处](../operations-management-suite/operations-management-suite-solutions.md)了解更多信息。 选择用于群集的每个解决方案可在同一 OMS 工作区及 Log Analytics 进行配置。 工作区允许使用自定义仪表板和数据可视化，以及对要收集、处理和分析的数据进行修改。

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>使用 Service Fabric 分析解决方案设置 OMS 工作区
建议将 Service Fabric 解决方案添加到 OMS 工作区中，因为它包含在平台和应用程序级别显示各种传入日志通道的仪表板，并能查询 Service Fabric 特定日志。 相对简单的 Service Fabric 解决方案如下所示，群集上部署了一个应用程序：

![OMS SF 解决方案](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

要对群集使用此方案，请参阅[设置 OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)。

## <a name="using-the-oms-agent"></a>使用 OMS 代理

建议使用 EventFlow 和 WAD 作为聚合解决方案，因为它们允许使用更加模块化方法，方便诊断和监视。 例如，若想从 EventFlow 更改输出，不需要更改实际检测，仅需对配置文件进行简单修改。 但如果决定使用 OMS Log Analytics 进行处理，需设置 [OMS 代理](../log-analytics/log-analytics-windows-agents.md)。 将容器部署到群集时，还应使用 OMS 代理，如下所述。 

相关步骤请访问[将 OMS 代理添加到群集](service-fabric-diagnostics-oms-agent.md)。

这样做的好处有：

* 性能计数器和指标端的数据更丰富
* 轻松配置从群集收集的度量值，而无需更新群集的配置。 可以在 OMS 门户中完成对代理设置的更改，代理将自动重启以匹配所需的配置。 若要配置 OMS 代理以获取特定性能计数器，请转到工作区的“主页”>“设置”>“数据”>“Windows 性能计数器”，选择想要收集的数据
* 相较于在 OMS/Log Analytics 提取数据前不得不存储数据，数据显示得更快。
* 监视容器则更加简单，因为它可以提取 Docker 日志（stdout、stderror）和统计信息（容器和节点级别的性能度量值）

此处最主要的考虑因素是，由于代理将与所有应用程序一同部署到群集，因此会稍微影响群集上应用程序的性能。

## <a name="monitoring-containers"></a>监视容器

将容器部署到 Service Fabric 群集前，建议同时设置群集与 OMS 代理，并将容器解决方案添加到 OMS 工作区以实现监视和诊断。 容器解决方案在工作区中看似这样：

![基本 OMS 仪表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理可收集数个特定于容器的日志，可在 OMS 中进行查询或用于直观的性能指示器。 收集的日志类型：

* ContainerInventory：显示有关容器位置、名称和图像的信息
* ContainerImageInventory：有关已部署映像的信息，包括 ID 或大小
* ContainerLog：特定的错误日志、stdout 等 docker 日志和其他条目
* ContainerServiceLog：已运行的 docker 守护程序命令
* Perf：性能计数器，包括容器 cpu、内存、网络流量、磁盘 i/o 和主机的自定义指标

[使用 OMS Log Analytics 监视容器](service-fabric-diagnostics-oms-containers.md)介绍如何为群集设置容器监视。 若要了解有关 OMS 容器解决方案的详细信息，请查看相关[文档](../log-analytics/log-analytics-containers.md)。

## <a name="next-steps"></a>后续步骤

浏览以下 OMS 工具和选项以根据需要自定义工作区：

* 对于本地群集，OMS 提供可用于向 OMS 发送数据的网关（HTTP 正向代理）。 有关更多信息，请参阅[使用 OMS 网关将无法访问 Internet 的计算机连接到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 配置 OMS，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能