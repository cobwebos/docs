---
title: 使用指标监视 Azure 数据资源管理器性能、运行状况 & 使用情况
description: 了解如何使用 Azure 数据资源管理器度量值监视群集的性能、运行状况和使用情况。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560298"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用指标监视 Azure 数据资源管理器性能、运行状况和使用情况

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 Azure 数据资源管理器度量值提供有关群集资源的运行状况和性能的关键指标。 使用本文中详述的指标来监视 Azure 数据资源管理器群集的运行状况和性能，具体方案为独立的指标。 你还可以使用度量值作为运营[Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)和[Azure 警报](/azure/azure-monitor/platform/alerts-metric-overview)的基础。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有帐户，可以创建一个[免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* [群集和数据库](create-cluster-database-portal.md)。

## <a name="using-metrics"></a>使用指标

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在 Azure 数据资源管理器群集中，选择 "**指标**" 以打开 "度量值" 窗格并在群集上开始分析。
    ![选择指标](media/using-metrics/select-metrics.png)。
1. 在 "度量值" 窗格中： ![度量值 "窗格](media/using-metrics/metrics-pane.png)
    1. 若要创建指标图表，请选择**指标**名称和每个指标的相关**聚合**。 **资源**和**指标命名空间**选取器已为 Azure 数据资源管理器群集预选择。 有关不同指标的详细信息，请参阅[支持的 Azure 数据资源管理器指标](#supported-azure-data-explorer-metrics)。
    1. 选择 "**添加度量值**" 可查看在同一图表中绘制多个度量值。
    1. 选择 " **+ 新建图表**" 以在一个视图中查看多个图表。
    1. 使用时间选取器更改时间范围（默认值：过去24小时）。
    1. 对于具有维度的指标，使用 " [**添加筛选器**" 和 "**应用拆分**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) "。
    1. 选择 "**固定到仪表板**"，将图表配置添加到仪表板，以便可以再次查看。
    1. 设置**新的警报规则**，以使用设置的条件可视化指标。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 在[警报规则创建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改这些设置。

有关使用[指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)的其他信息。

## <a name="supported-azure-data-explorer-metrics"></a>支持的 Azure 数据资源管理器指标

根据使用情况，支持的 Azure 数据资源管理器度量值分为不同的类别。 

### <a name="cluster-health-metrics"></a>群集运行状况指标

群集运行状况指标跟踪群集的常规运行状况。 这包括资源以及引入利用率和响应能力。

**指标** | **单位** | **聚合** | **指标描述** | **Dimensions** |
|---|---|---|---|---|
| 缓存利用率 | 百分比 | Avg、Max、Min | 群集当前正在使用的已分配缓存资源的百分比。 缓存是根据定义的缓存策略为用户活动分配的 SSD 大小。 对于群集，平均缓存利用率为80% 或更低。 如果平均缓存利用率高于80%，则应将群集[扩展](manage-cluster-vertical-scaling.md)到存储优化定价层，或[扩大](manage-cluster-horizontal-scaling.md)到更多实例。 或者，改编缓存策略（缓存中的天数更少）。 如果缓存利用率超过100%，则根据缓存策略缓存的数据大小越大，群集上的缓存的总大小就越大。 | 无 |
| CPU | 百分比 | Avg、Max、Min | 群集中的计算机当前正在使用的已分配计算资源的百分比。 对于群集，平均 CPU 为80% 或更低。 CPU 的最大值为100%，这意味着没有额外的计算资源可用于处理数据。 当群集不能正常运行时，请检查 CPU 的最大值，以确定是否有特定的 Cpu 被阻止。 | 无 |
| 引入利用率 | 百分比 | Avg、Max、Min | 用于从分配的资源（容量策略）中引入数据以执行引入的实际资源的百分比。 默认容量策略不超过512个并发引入操作或引入中投入的群集资源的75%。 80% 或更低的平均引入利用率是群集的可持续状态。 引入使用率的最大值为100%，这意味着使用所有群集引入能力，并且可能会导致引入队列。 | 无 |
| 保持活动状态 | Count | Avg | 跟踪群集的响应能力。 完全响应的群集返回值1，阻止或断开连接的群集返回0。 |
| 阻止的命令总数 | Count | Avg、Max、Min、Sum | 由于已达到允许的最大并发（并行）命令数，导致群集中的限制（拒绝）命令数。 | 无 |
| 总区数 | Count | Avg、Max、Min、Sum | 群集中的数据区总数。 此指标的变化可能意味着大规模的数据结构更改和群集上的高负载，因为合并数据区是一个 CPU 繁重的活动。 | 无 |
| | | | |

### <a name="export-health-and-performance-metrics"></a>导出运行状况和性能指标

导出运行状况和性能指标跟踪导出操作的常规运行状况和性能，如迟到、结果、记录数和利用率。

**指标** | **单位** | **聚合** | **指标描述** | **Dimensions** |
|---|---|---|---|---|
连续导出已导出记录的数目    | Count | SUM | 所有连续导出作业中导出的记录数。 | 无 |
连续导出最大迟到 |    Count   | Max   | 群集中的连续导出作业报告的迟到（分钟）。 | 无 |
连续导出挂起计数 | Count | Max   | 挂起的连续导出作业的数目。 这些作业已准备好运行，但在队列中等待，这可能是由于容量不足所致。 
连续导出结果    | Count |   Count   | 每个连续导出运行的失败/成功结果。 | ContinuousExportName |
导出利用率 |    百分比 | Max   | 导出使用的容量，超出群集中的总导出容量（0到100）。 | 无 |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>引入运行状况和性能指标

引入运行状况和性能指标跟踪引入操作（如延迟、结果和卷）的一般运行状况和性能。

**指标** | **单位** | **聚合** | **指标描述** | **Dimensions** |
|---|---|---|---|---|
| 处理的事件数（适用于事件/IoT 中心） | Count | Max、Min、Sum | 从事件中心读取并由群集处理的事件总数。 事件被拆分为已拒绝的事件和群集引擎接受的事件。 | EventStatus |
| 引入延迟 | 秒 | Avg、Max、Min | 数据引入的延迟时间，从在群集中接收数据到查询准备就绪。 引入延迟时间取决于引入方案。 | 无 |
| 引入结果 | Count | Count | 失败和成功的引入操作的总数。 使用 "**应用拆分**" 创建成功和失败结果的存储桶，并分析维度（**值** > **状态**）。| IngestionResultDetails |
| 引入卷（MB） | Count | Max、Sum | 压缩前引入到群集的数据的总大小（以 MB 为单位）。 | 数据库 |
| | | | |  

### <a name="query-performance"></a>查询性能

查询性能指标跟踪查询持续时间和并发或限制查询的总数。

**指标** | **单位** | **聚合** | **指标描述** | **Dimensions** |
|---|---|---|---|---|
| 查询持续时间 | 毫秒 | Avg、Min、Max、Sum | 接收到查询结果之前的总时间（不包括网络延迟）。 | QueryStatus |
| 并发查询总数 | Count | Avg、Max、Min、Sum | 群集中并行运行的查询数。 此指标是估计群集上的负载的好方法。 | 无 |
| 限制查询总数 | Count | Avg、Max、Min、Sum | 群集中限制（拒绝）的查询数。 并发查询策略中定义了允许的最大并发（并行）查询数。 | 无 |
| | | | |

### <a name="streaming-ingest-metrics"></a>流式处理引入指标

流式处理引入指标跟踪流式处理引入数据和请求速率、持续时间和结果。

**指标** | **单位** | **聚合** | **指标描述** | **Dimensions** |
|---|---|---|---|---|
流引入数据速率 |    Count   | RateRequestsPerSecond | 引入到群集的总数据量。 | 无 |
流摄取持续时间   | 毫秒  | Avg、Max、Min | 所有流式处理引入请求的总持续时间。 | 无 |
流式处理引入请求速率   | Count | Count、Avg、Max、Min、Sum | 流式引入请求总数。 | 无 |
流式处理引入结果 | Count | Avg   | 按结果类型的流式引入请求总数。 | 结果 |
| | | | |

有关[受支持的 Azure 数据资源管理器群集指标](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他信息。


## <a name="next-steps"></a>后续步骤

* [教程：在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code)
* [使用诊断日志监视 Azure 数据资源管理器引入操作](/azure/data-explorer/using-diagnostic-logs)
* [快速入门：在 Azure 数据资源管理器中查询数据](web-query-data.md)
