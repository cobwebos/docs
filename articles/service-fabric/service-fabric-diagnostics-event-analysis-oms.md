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
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2017
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

可通过两种方法预配和配置 OMS 工作区：通过 Resource Manager 模板或直接从 Azure 应用商店预配和配置。 部署群集时使用第一种方法，若已在启用了诊断的情况下部署有群集，则使用第二种。

### <a name="deploying-oms-using-a-resource-management-template"></a>使用 Resource Manager 模板部署 OMS

当使用资源管理器模板部署群集时，该模板还会创建新的 OMS 工作区，向其添加 Service Fabric 解决方案，并将其配置为从适当的存储表中读取数据。

>[!NOTE]
>若要使其有效，则必须启用诊断以使 Azure 存储表存在，让 OMS/Log Analytics 能从中读取数据。

[此处](https://azure.microsoft.com/resources/templates/service-fabric-oms/)是示例模板，可根据需求使用和修改，它将执行上述操作。 如果想要更多选择，可在 [Service Fabric 和OMS 模板](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)处查阅更多模板，它们根据设置 OMS 工作区所处的阶段为你提供其他选项。

### <a name="deploying-oms-using-through-azure-marketplace"></a>通过 Azure 应用商店部署 OMS

若要在部署群集之后添加 OMS 工作区，请转到 Azure 应用商店，查找“Service Fabric 分析”。

![应用商店中的 OMS SF 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* 单击“创建”
* 在 Service Fabric 分析创建窗口中，针对“OMS 工作区”字段单击“选择工作区”，然后单击“创建新工作区”。 填写必需的条目，此处仅要求 Service Fabric 群集和 OMS 工作区的订阅相同。 验证条目后，OMS 工作区将启动部署。 此过程应该只需要几分钟的时间。
* 完成后，在 Service Fabric 分析创建窗口的底部再次单击“创建”。 请确保新工作区显示在 OMS 工作区下。 这会将解决方案添加到刚刚创建的工作区中。


尽管这会将解决方案添加到工作区中，但工作区仍然需要连接到来自群集的诊断数据。 导航到在其中创建 Service Fabric 分析解决方案的资源组。 应该会看到 ServiceFabric(\<nameOfOMSWorkspace\>)。

* 单击该解决方案以导航到其概述页面，可以在此处更改解决方案设置、工作区设置，并导航到 OMS 门户。
* 在左侧导航菜单上，单击“工作区数据源”下的“存储帐户日志”。

    ![门户中的 Service Fabric 分析解决方案](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* 在“存储帐户日志”页上，单击顶部的“添加”将群集的日志添加到工作区。
* 单击“存储帐户”添加群集中创建的相应帐户。 如果使用了默认名称，则存储帐户名为 sfdg\<resourceGroupName\>。 还可以通过检查用于部署群集的 Azure 资源管理器模板，以及检查用于 `applicationDiagnosticsStorageAccountName` 的值来确认上述操作。 如果帐户名称未显示，则可能还需要向下滚动并单击“加载更多”。 在显示帐户名称后单击相应的存储帐户名称以选中它。
* 接下来，将需要指定“数据类型”，应为“Service Fabric 事件”。
* 应将源自动设置为 WADServiceFabric\*EventTable。
* 单击“确定”将工作区连接到群集的日志。

    ![将存储帐户日志添加到 OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* 该帐户现在应显示为工作区数据源的存储帐户日志的一部分。

因此，现在已将 Service Fabric 分析解决方案添加到 OMS Log Analytics 工作区中，该工作区现已正确连接到群集的平台和应用程序日志表。 可以用相同的方式将其他源添加到工作区中。

## <a name="using-the-oms-agent"></a>使用 OMS 代理

建议使用 EventFlow 和 WAD 作为聚合解决方案，因为它们允许使用更加模块化方法，方便诊断和监视。 例如，若想从 EventFlow 更改输出，不需要更改实际检测，仅需对配置文件进行简单修改。 然而，若决定投资使用 OMS（不需为使用的唯一平台，但至少为所使用平台之一），且愿意继续使用它进行事件分析，建议尝试设置 [OMS 代理](../log-analytics/log-analytics-windows-agents.md)。 将容器部署到群集时，还应使用 OMS 代理，如下所述。

执行此操作的过程相对容易，只需在 Resource Manager 模板中添加代理作为虚拟机规模集扩展，确保其安装在每个节点上。 可以为运行 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) 的群集找到使用 Service Fabric 解决方案（如上所述）部署 OMS 工作区并添加代理到节点的示例资源管理器模板。

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

本文介绍如何为群集设置容器监视。 若要了解有关 OMS 容器解决方案的详细信息，请查看相关[文档](../log-analytics/log-analytics-containers.md)。

若要在工作区中设置容器解决方案，请确保已遵循上文所述步骤在群集结点上部署 OMS 代理。 群集准备就绪后，请在其上部署一个容器。 请记住，首次将容器映像部署到群集时，需要几分钟才能下载映像，具体时间取决于映像大小。

在 Azure Marketplace 中的“监视 + 管理”类别下，搜索“容器监视解决方案”并创建应该显示的“容器监视解决方案”结果。

![添加容器解决方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

创建过程中，需使用 OMS 工作区。 选择使用上述部署创建的工作区。 此步骤在 OMS 工作区中添加容器解决方案，并由模板部署的 OMS 代理进行自动检测。 代理开始在群集中收集容器进程的数据，大约 15 分钟后，即可看到带数据的解决方案亮起，如上方仪表板图像所示。


## <a name="next-steps"></a>后续步骤

浏览以下 OMS 工具和选项以根据需要自定义工作区：

* 对于本地群集，OMS 提供可用于向 OMS 发送数据的网关（HTTP 正向代理）。 有关更多信息，请参阅[使用 OMS 网关将无法访问 Internet 的计算机连接到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 配置 OMS，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能