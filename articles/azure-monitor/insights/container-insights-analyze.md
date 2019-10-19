---
title: 使用适用于容器的 Azure Monitor 监视 AKS 群集性能 | Microsoft Docs
description: 本文介绍如何使用适用于容器的 Azure Monitor 查看和分析性能和日志数据。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/17/2019
ms.openlocfilehash: a65951a90767acce5570244af8a9250845e12def
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554239"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>使用适用于容器的 Azure Monitor 了解 AKS 群集性能
对于容器 Azure Monitor，可以使用性能图表和运行状况状态从两个角度监视 Azure Kubernetes 服务（AKS）群集的工作负荷。 可以直接从 AKS 群集进行监视，也可以从 Azure Monitor 监视订阅中的所有 AKS 群集。 监视特定的 AKS 群集时，还可以查看 Azure 容器实例。

本文将帮助你了解两个角度，以及 Azure Monitor 如何帮助你快速评估、调查和解决检测到的问题。

有关如何为容器启用 Azure Monitor 的信息，请参阅[容器的板载 Azure Monitor](container-insights-onboard.md)。

Azure Monitor 提供了一个多群集视图，其中显示了在订阅中跨资源组部署的、运行 Linux 和 Windows Server 2019 的所有受监视 AKS 群集的运行状况状态。 其中显示了解决方案未监视的 AKS 群集。 你可以立即了解群集运行状况，并在此处向下钻取到 "节点和控制器性能" 页，或导航以查看群集的性能图表。 对于发现并识别为不受监视的 AKS 群集，可以随时对其启用监视。 

与 Linux 群集相比，使用容器 Azure Monitor 来监视 Windows Server 群集的主要区别如下：

- 内存 RSS 指标不适用于 Windows 节点和容器。
- 磁盘存储容量信息不适用于 Windows 节点。
- Windows 容器日志除外，提供实时日志支持。
- 仅监视 pod 环境，而不监视 Docker 环境。
- 对于预览版本，最多支持30个 Windows Server 容器。 此限制不适用于 Linux 容器。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。 

## <a name="multi-cluster-view-from-azure-monitor"></a>从 Azure Monitor 获得的多群集视图

若要查看部署的所有 AKS 群集的运行状况状态，请从 Azure 门户的左窗格中选择 "**监视**"。 在“见解”部分，选择“容器”。 

![Azure Monitor 多群集仪表板示例](./media/container-insights-analyze/azmon-containers-multiview.png)

在 "**监视的群集**" 选项卡上，你将了解以下内容：

- 多少群集处于严重或不正常状态，以及有多少群集处于正常状态或未进行报告（称为未知状态）。
- 所有[Azure Kubernetes 引擎（AKS）](https://github.com/Azure/aks-engine)部署是否正常。
- 每个群集部署的节点数和用户数和系统箱数。
- 有多少可用磁盘空间，以及是否有容量问题。

包含的运行状况有： 

* **正常**： VM 未检测到任何问题，并且该 VM 正在运行。 
* **严重**：检测到一个或多个关键问题，必须解决这些问题，才能按预期还原正常操作状态。
* **警告**：检测到一个或多个必须解决的问题，或者运行状况条件可能变得严重。
* **未知**：如果服务无法与节点或 pod 建立连接，状态将更改为 "未知" 状态。
* **找不到**：工作区、包含此解决方案的工作区的资源组或订阅已被删除。
* **未授权**：用户没有读取工作区中数据所需的权限。
* **错误**：尝试从工作区读取数据时出错。
* **错误配置**：指定工作区中没有正确配置容器 Azure Monitor。
* **无数据**：过去30分钟未向工作区报告数据。

运行状况状态将总体群集状态计算为三种状态中*最差的*状态，但有一个例外。 如果这三个状态中有任何一个是未知的，则总体群集状态显示为 "**未知**"。 

下表提供了用于控制多群集视图上监视群集的运行状况状态的计算细目。

| |状态 |可用性 |  
|-------|-------|-----------------|  
|**用户 pod**| | |  
| |正常 |100% |  
| |警告 |90 - 99% |  
| |严重 |<90% |  
| |未知 |如果未在过去 30 分钟报告 |  
|**系统 pod**| | |  
| |正常 |100% |
| |警告 |N/A |
| |严重 |<100% |
| |未知 |如果未在过去 30 分钟报告 |
|**Node** | | |
| |正常 |>85% |
| |警告 |60 - 84% |
| |严重 |<60% |
| |未知 |如果未在过去 30 分钟报告 |

在群集列表中，可以通过选择群集的名称向下钻取到 "**群集**" 页。 然后，通过选择该特定群集的 "**节点**" 列中的节点的汇总，中转到 "**节点**性能" 页。 或者，可以通过选择 "**用户箱**" 或 "**系统**pod" 列的汇总来向下钻取到 "**控制器**性能" 页。  

## <a name="view-performance-directly-from-an-aks-cluster"></a>直接从 AKS 群集查看性能

通过从左窗格中选择 "**见解**"，可以直接从 AKS 群集访问对容器 Azure Monitor。 AKS 群集的相关信息分为四个方面：

- 群集
- 节点数 
- 控制器 
- 容器

选择 " **Insights**  > **群集**" 时，将打开默认页。 四行性能图表显示群集的关键性能指标。 

![“群集”选项卡上的性能图表示例](./media/container-insights-analyze/containers-cluster-perfview.png)

性能图表显示四个性能指标：

- **节点 cpu 利用率 &nbsp; %** ：整个群集的 cpu 利用率的聚合透视。 若要筛选时间范围的结果，请在图表上方的百分位数选择器中选择**Avg**、 **Min**、第**50**、 **90**、 **95**或**Max** 。 筛选器可以单独使用，也可以组合使用。 
- **节点内存利用率 &nbsp;(%)** ：从聚合视角反映整个群集的内存利用率。 若要筛选时间范围的结果，请在图表上方的百分位数选择器中选择**Avg**、 **Min**、第**50**、 **90**、 **95**或**Max** 。 筛选器可以单独使用，也可以组合使用。 
- **节点数**：Kubernetes 提供的节点计数和状态。 表示的群集节点的状态为 "已完成"、"就绪" 和 "未就绪"。 可以单独筛选它们，也可以将其合并到图表上方的选择器中。 
- **活动 pod 计数**： Kubernetes 中的 pod 计数和状态。 所表示的 pod 状态总数、挂起、正在运行、未知、成功或失败。 可以单独筛选它们，也可以将其合并到图表上方的选择器中。 

使用向左箭头和向右箭头键可循环浏览图表上的每个数据点。 使用向上键和向下键可循环遍历百分位的行。 选择任何一个图表右上角的固定图标，以将所选图表固定到最近查看的 Azure 仪表板。 在仪表板中，可以调整图表的大小和重新定位图表。 从仪表板中选择图表将重定向到为容器 Azure Monitor，并加载正确的作用域和视图。

容器 Azure Monitor 还支持 Azure Monitor[指标资源管理器](../platform/metrics-getting-started.md)，您可以在其中创建自己的绘图图、关联和调查趋势以及固定到仪表板。 在指标资源管理器中，还可以使用设置的条件，将指标可视化为[基于指标的警报规则](../platform/alerts-metric.md)的基础。 

## <a name="view-container-metrics-in-metrics-explorer"></a>在指标资源管理器中查看容器指标

在指标资源管理器中，可以查看容器 Azure Monitor 的聚合节点和 pod 利用率指标。 下表总结了详细信息，以帮助你了解如何使用指标图表来可视化容器指标。

|命名空间 | 指标 | 描述 | 
|----------|--------|-------------|
| 见解：容器/节点 | |
| | cpuUsageMillicores | 整个群集中 CPU 使用率的聚合度量值。 它是 CPU 核心拆分为1000单位（milli = 1000）。 用于确定在多个应用程序可能使用一个内核的容器中内核的使用情况。| 
| | cpuUsagePercentage | 群集平均 CPU 使用率（以百分比度量）。|
| | memoryRssBytes | 使用的容器 RSS 内存（字节）。| 
| | memoryRssPercentage | 用于百分比的容器 RSS 内存。|
| | memoryWorkingSetBytes | 容器工作集内存已使用。| 
| | memoryWorkingSetPercentage | 容器工作集内存用百分比表示。 | 
| | nodesCount | Kubernetes 中的节点计数。|
| 见解/容器 | |
| | PodCount | Kubernetes 中的 pod 计数。|

您可以[拆分](../platform/metrics-charts.md#apply-splitting-to-a-chart)度量值以按维度进行查看，并将其不同段的相互比较。 对于节点，可以按*主机*维度对图表进行分段。 在 pod 中，可以按以下尺寸对其进行细分：

* 控制器
* Kubernetes 命名空间
* 节点
* 阶段

## <a name="analyze-nodes-controllers-and-container-health"></a>分析节点、控制器和容器运行状况

切换到 "**节点**"、"**控制器**" 和 "**容器**" 选项卡时，会自动在页面右侧显示属性窗格。 它显示选定项的属性，其中包括你定义用于组织 Kubernetes 对象的标签。 选择 Linux 节点后，"**本地磁盘容量**" 部分还会显示可用磁盘空间，以及显示给节点的每个磁盘所用的百分比。 在窗格中选择 " **>>** " 链接以查看或隐藏窗格。

![示例 Kubernetes "透视" 属性窗格](./media/container-insights-analyze/perspectives-preview-pane-01.png)

在层次结构中展开对象时，属性窗格将根据所选对象进行更新。 在该窗格中，你还可以通过选择窗格顶部的 "**查看 Kubernetes 事件日志**" 链接来查看具有预定义日志搜索的 Kubernetes 事件。 有关如何查看 Kubernetes 日志数据的详细信息，请参阅[搜索日志以分析数据](container-insights-log-search.md)。 查看群集资源时，可以实时查看容器日志和事件。 有关此功能以及授予和控制访问权限所需的配置的详细信息，请参阅[使用容器 Azure Monitor 实时查看日志](container-insights-live-logs.md)。 

使用页面顶部的 " **+ 添加筛选器**" 选项，按**服务**、**节点**、**命名空间**或**节点池**筛选视图的结果。 选择筛选器作用域后，选择 "**选择值**" 字段中显示的值之一。 配置筛选器后，它将在全局范围内应用，同时查看 AKS 群集的任何透视。 公式只支持等号。 可以在第一个筛选器的基础上添加更多的筛选器，进一步缩小结果范围。 例如，如果按**节点**指定筛选器，则只能为第二个筛选器选择 "**服务**" 或 "**命名空间**"。

![通过筛选器缩小结果范围的示例](./media/container-insights-analyze/add-filter-option-01.png)

选择另一个选项卡时，将继续应用一个选项卡中的筛选器。 在选择指定筛选器旁的**x**符号后，将其删除。 

切换到 "**节点**" 选项卡，行层次结构遵循 Kubernetes 对象模型，该对象模型从群集中的节点开始。 展开节点以查看节点上运行的一个或多个 pod。 如果有多个容器分组到 pod，它们将显示为层次结构中的最后一行。 如果主机具有处理器或内存压力，则还可以查看主机上正在运行的非 pod 相关工作负荷的数量。

![性能视图中 Kubernetes 节点层次结构的示例](./media/container-insights-analyze/containers-nodes-view.png)

运行 Windows Server 2019 OS 的 windows Server 容器显示在列表中所有基于 Linux 的节点之后。 展开 Windows Server 节点时，可以查看在该节点上运行的一个或多个 pod 和容器。 选择节点后，"属性" 窗格将显示版本信息。 由于 Windows Server 节点没有安装代理，代理信息被排除。 

![列出的包含 Windows Server 节点的节点层次结构示例](./media/container-insights-analyze/nodes-view-windows.png) 

运行 Linux OS 的 Azure 容器实例虚拟节点显示在列表中最后一个 AKS 群集节点之后。 展开容器实例虚拟节点时，可以查看在该节点上运行的一个或多个容器实例箱和容器。 只为 pod 收集和报告指标。

![列出了容器实例的示例节点层次结构](./media/container-insights-analyze/nodes-view-aci.png)

从展开的节点，可以从节点上运行的 pod 或容器向下钻取到控制器，以查看为该控制器筛选的性能数据。 选择特定节点的 "**控制器**" 列下的值。
 
![性能视图中从节点到控制器的示例向下钻取](./media/container-insights-analyze/drill-down-node-controller.png)

选择页面顶部的 "控制器" 或 "容器"，查看这些对象的状态和资源利用率。 若要查看内存使用率，请在 "**指标**" 下拉列表中选择 "**内存 RSS** " 或 "**内存工作集**"。 仅 Kubernetes 1.8 版和更高版本支持**内存 RSS**。 否则，看到的 **Min&nbsp;%** 值会显示为 *NaN&nbsp;%* ，它表示未定义或无法表示的值的数值数据类型值。

![容器节点性能视图](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**内存工作集**显示随附的内存和虚拟内存（缓存），并且是应用程序使用的总大小。 **内存 RSS**只显示主内存（而不是常驻内存）。 此指标显示可用内存的实际容量。 驻留内存和虚拟内存之间有何区别？

- 驻留内存或主内存是可用于群集节点的实际计算机内存量。

- 在内存压力下，虚拟内存是操作系统用于将数据从内存交换到磁盘的保留硬盘空间（缓存），然后在需要时将数据提取回内存。

默认情况下，性能数据基于过去六个小时，但你可以使用左上角的 " **TimeRange** " 选项更改该窗口。 您还可以通过在百分点选择器中选择 "**最小值**"、"**平均值**"、"第**50** **个"、** **"最**大值"、"**最大值**" 来筛选时间范围 

![用于数据筛选的百分位选择](./media/container-insights-analyze/containers-metric-percentile-filter.png)

当你将鼠标悬停在 "**趋势**" 列下的 "条形图" 上时，每个条形显示 CPU 或内存使用量，具体取决于选择的指标，在15分钟的示例中。 通过键盘选择趋势图表后，使用 Alt + Page up 键或 Alt + Page down 键分别循环遍历每个条形。 您可以获得与在该条形上悬停的内容相同的详细信息。

![趋势条形图悬停-over 示例](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

在下一个示例中，对于列表中的第一个节点， *aks-nodepool1-* ，**容器**的值为9。 此值是已部署的容器总数的汇总。

![每个节点的容器汇总示例](./media/container-insights-analyze/containers-nodes-containerstotal.png)

此信息可以帮助你快速确定群集中的节点之间是否具有适当的容器平衡。 

下表描述了查看 "**节点**" 选项卡时显示的信息。

| 柱形图​​ | 描述 | 
|--------|-------------|
| 名称 | 主机的名称。 |
| 状态 | 节点状态的 Kubernetes 视图。 |
| 最小 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; %  | 基于所选时段百分位的平均节点百分比。 |
| Min、Avg、第50、90、95、Max | 所选时间段内基于百分位的平均节点实际值。 平均值从为节点设置的 CPU/内存限制度量。 对于 pod 和容器，它是主机报告的平均值。 |
| 容器 | 容器数量。 |
| 运行时间 | 表示节点启动或重启后的时间。 |
| 控制器 | 仅适用于容器和 Pod。 它显示了它所在的控制器。 并非所有 Pod 都在控制器中，因此有些 Pod 可能会显示 **N/A**。 | 
| 趋势分钟 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; % | 条形图趋势表示控制器的平均百分位指标百分比。 |

在选择器中，选择“控制器”。

![选择控制器视图](./media/container-insights-analyze/containers-controllers-tab.png)

在此处，可以查看控制器和容器实例虚拟节点控制器或未连接到控制器的虚拟节点盒的性能运行状况。

![\<Name > 控制器性能视图](./media/container-insights-analyze/containers-controllers-view.png)

行层次结构从控制器开始。 展开控制器时，查看一个或多个 pod。 展开 Pod，最后一行显示分组到 Pod 的容器。 从展开的控制器中，你可以向下钻取到其上运行的节点，以查看为该节点筛选的性能数据。 列表中最后列出了未连接到控制器的容器实例。

![列出了容器实例 Pod 的示例控制器层次结构](./media/container-insights-analyze/controllers-view-aci.png)

选择特定控制器的 "**节点**" 列下的值。

![性能视图中从节点到控制器的示例向下钻取](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看控制器时显示的信息。

| 柱形图​​ | 描述 | 
|--------|-------------|
| 名称 | 控制器的名称。|
| 状态 | 容器在运行完成后的汇总状态，状态为 *"正常"、"* 已*终止*" *、* "*已停止*" 或 "已*暂停*"。 如果容器正在运行，但状态未正确显示，或者代理未获取该状态，且未响应30分钟以上，则状态为 "*未知*"。 下表提供了状态图标的其他详细信息。|
| 最小 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; %| 每个实体在选定指标和百分位的平均百分比的汇总平均值。 |
| Min、Avg、第50、90、95、Max  | 容器在选定百分位的平均 CPU millicore 或内存性能汇总。 平均值根据为 Pod 设置的 CPU/内存限制进行计算。 |
| 容器 | 控制器或 Pod 的容器总数。 |
| 重启数 | 容器重启计数汇总。 |
| 运行时间 | 表示容器启动后的时间。 |
| 节点 | 仅适用于容器和 Pod。 它显示了它所在的控制器。 | 
| 趋势分钟 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; % | 条形图趋势表示控制器的平均百分位指标。 |

"状态" 字段中的图标表示容器的联机状态。
 
| 图标 | 状态 | 
|--------|-------------|
| ![“就绪”运行状态图标](./media/container-insights-analyze/containers-ready-icon.png) | 正在运行（就绪）|
| !["等待或暂停状态" 图标](./media/container-insights-analyze/containers-waiting-icon.png) | “正在等待”或“已暂停”|
| ![“上次报告正在运行”状态图标](./media/container-insights-analyze/containers-grey-icon.png) | 上次报告运行，但未响应30分钟以上|
| ![成功状态图标](./media/container-insights-analyze/containers-green-icon.png) | 成功停止或无法停止|

状态图标显示的计数基于 Pod 提供的数据。 它显示最差的两个状态。将鼠标悬停在状态上时，它显示容器中所有 Pod 的汇总状态。 如果没有就绪状态，状态值会显示 **(0)** 。

在选择器中，选择“容器”。

![选择容器视图](./media/container-insights-analyze/containers-containers-tab.png)

在此处可查看 Azure Kubernetes 和 Azure 容器实例容器的性能运行状况。 

![\<Name > 容器性能视图](./media/container-insights-analyze/containers-containers-view.png)

从容器中，可以向下钻取到某个 pod 或节点来查看针对该对象筛选的性能数据。 选择特定容器的 " **Pod** " 或 "**节点**" 列下的值。

![在性能视图中从节点向下钻取到容器的示例](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看容器时显示的信息。

| 柱形图​​ | 描述 | 
|--------|-------------|
| 名称 | 控制器的名称。|
| 状态 | 容器状态（如果有）。 接下来的表格提供状态图标的更多详细信息。|
| 最小 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; % | 每个实体在选定指标和百分位的平均百分比汇总。 |
| Min、Avg、第50、90、95、Max | 容器在选定百分位的平均 CPU millicore 或内存性能汇总。 平均值根据为 Pod 设置的 CPU/内存限制进行计算。 |
| Pod | Pod 驻留的容器。| 
| 节点 |  容器驻留的节点。 | 
| 重启数 | 表示容器启动后的时间。 |
| 运行时间 | 表示容器启动或重启后的时间。 |
| 趋势分钟 &nbsp;%，平均 &nbsp;%，第50个 &nbsp;%，90 &nbsp;%，95 &nbsp;%，最大 &nbsp; % | 条形图趋势表示容器的平均百分位指标百分比。 |

"状态" 字段中的图标指示盒的联机状态，如下表所述。
 
| 图标 | 状态 |  
|--------|-------------|  
| ![“就绪”运行状态图标](./media/container-insights-analyze/containers-ready-icon.png) | 正在运行（就绪）|  
| !["等待或暂停状态" 图标](./media/container-insights-analyze/containers-waiting-icon.png) | “正在等待”或“已暂停”|  
| ![“上次报告正在运行”状态图标](./media/container-insights-analyze/containers-grey-icon.png) | 上次报告正在运行但已超过 30 分钟未响应|  
| ![“已终止”状态图标](./media/container-insights-analyze/containers-terminated-icon.png) | 成功停止或无法停止|  
| ![“已失败”状态图标](./media/container-insights-analyze/containers-failed-icon.png) | “已失败”状态 |  

## <a name="workbooks"></a>工作簿

工作簿将文本、 [日志查询](../log-query/query-language.md)、[指标](../platform/data-platform-metrics.md)和参数合并到丰富的交互式报表中。 有权访问相同 Azure 资源的其他团队成员都可编辑工作簿。

容器 Azure Monitor 包括以下四个工作簿：

- **磁盘容量**：为容器中的节点显示的每个磁盘提供交互式磁盘使用情况图表，如下所示：

    - 所有磁盘的磁盘使用百分比。
    - 所有磁盘的可用磁盘空间。
    - 显示每个节点的磁盘、其已用空间百分比、已用空间百分比趋势、可用磁盘空间（GiB）和可用磁盘空间趋势（GiB）的网格。 当在表中选择某一行时，已用空间的百分比和可用磁盘空间（GiB）将显示在该行的下面。 

- **磁盘 IO**：通过以下透视为容器中的节点显示的每个磁盘提供交互式磁盘使用情况图表：

    - 跨所有磁盘的磁盘 i/o，按读取字节数/秒，写入字节数/秒，读取和写入字节数/秒趋势。
    - 8个性能图表显示关键性能指标，有助于度量和识别磁盘 i/o 瓶颈。

- **Kubelet**：包括两个显示关键节点操作统计信息的网格：

    - 概述节点网格汇总了每个节点的总操作、错误总数以及每个节点的按百分比和趋势的成功操作。
    - 概述按操作类型汇总了操作总计、错误总数和成功操作除以百分比和趋势的每个操作。

- **网络**：显示每个节点的网络适配器的交互式网络使用情况图表，并且网格显示关键性能指标以帮助测量网络适配器的性能。

您可以通过从 "**查看工作簿**" 下拉列表中选择每个工作簿来访问这些工作簿。

!["查看工作簿" 下拉列表](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>后续步骤

- 查看[使用容器 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何创建高 CPU 和内存使用率的警报，以支持 DevOps 或操作过程和过程。
- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
