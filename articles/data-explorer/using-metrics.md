---
title: 监视 Azure 数据资源管理器性能、运行状况&使用指标
description: 了解如何使用 Azure 数据资源管理器指标来监视群集的性能、运行状况和使用情况。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560298"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用指标监视 Azure 数据资源管理器的性能、运行状况和使用情况

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 Azure 数据资源管理器指标提供群集资源运行状况和性能的关键指标。 使用本文中详述的独立指标可以监视特定方案中 Azure 数据资源管理器群集的运行状况和性能。 还可以将指标用作正常运行的 [Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)和 [Azure 警报](/azure/azure-monitor/platform/alerts-metric-overview)的基础。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有，可以创建[一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* [群集和数据库](create-cluster-database-portal.md)。

## <a name="using-metrics"></a>使用指标

1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 在 Azure 数据资源管理器群集中，选择“指标”打开指标窗格，开始对群集进行分析。****
    ![选择指标](media/using-metrics/select-metrics.png)。
1. 在"指标"窗格中![：指标窗格](media/using-metrics/metrics-pane.png)
    1. 若要创建指标图表，请选择**指标**名称和每个指标的相关**聚合**。 系统为 Azure 数据资源管理器群集预先选择了“资源”和“指标命名空间”选取器。******** 有关不同指标的详细信息，请参阅[受支持的 Azure 数据资源管理器指标](#supported-azure-data-explorer-metrics)。
    1. 选择 **"添加指标**"可查看在同一图表中绘制的多个指标。
    1. 选择 **= 新图表**可查看一个视图中的多个图表。
    1. 使用时间选取器更改时间范围（默认：过去 24 小时）。
    1. 对包含维度的指标使用[**添加筛选器**和**应用拆分**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting)。
    1. 选择“固定到仪表板”可将图表配置添加到仪表板，以便可以再次查看它。****
    1. 设置**新的警报规则**可以使用设置的条件将指标可视化。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 在[警报规则创建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改这些设置。

有关使用[指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)的更多信息。

## <a name="supported-azure-data-explorer-metrics"></a>支持的 Azure 数据资源管理器指标

可根据用法将支持的 Azure 数据资源管理器指标划分为不同的类别。 

### <a name="cluster-health-metrics"></a>群集运行状况指标

群集运行状况指标跟踪群集的一般运行状况。 这包括资源和引入利用率以及响应能力。

**指标** | **单位** | **聚集** | **度量值说明** | **尺寸** |
|---|---|---|---|---|
| 缓存利用率 | 百分比 | Avg、Max、Min | 群集当前使用的已分配缓存资源百分比。 缓存是为用户活动分配的、符合定义的缓存策略的 SSD 大小。 80% 或更低的平均缓存利用率可以维持群集的正常状态。 如果平均缓存利用率超过 80%时，则群集应[纵向扩展](manage-cluster-vertical-scaling.md)到存储优化的定价层级，或[横向扩展](manage-cluster-horizontal-scaling.md)为更多实例。 也可以调整缓存策略（减少缓存的天数）。 如果缓存利用率超过 100%，则根据缓存策略缓存的数据大小将大于群集上的缓存总大小。 | 无 |
| CPU | 百分比 | Avg、Max、Min | 群集中的计算机当前使用的已分配计算资源百分比。 80% 或更低的平均 CPU 利用率可以维持群集的正常状态。 最大 CPU 利用率值为 100%，表示没有更多的计算资源可用于处理数据。 如果某个群集的性能不佳，请检查最大 CPU 利用率值，以确定特定的 CPU 是否阻塞。 | 无 |
| 引入利用率 | 百分比 | Avg、Max、Min | 用于从容量策略中分配的所有资源引入数据，以执行引入的实际资源百分比。 默认的容量策略是不超过 512 个并发的引入操作，或者不超过引入中投入的群集资源数的 75%。 80% 或更低的平均引入利用率可以维持群集的正常状态。 最大的引入利用率值为 100%，表示使用整个群集引入能力，这可能会生成引入队列。 | 无 |
| 保持活动状态 | Count | Avg | 跟踪群集的响应度。 完全可响应的群集将返回值 1，受阻或断开连接的群集将返回 0。 |
| 受限制的命令总数 | Count | Avg、Max、Min、Sum | 由于达到了允许的最大并发（并行）命令数，而在群集中限制（拒绝）的命令数。 | 无 |
| 盘区总数 | Count | Avg、Max、Min、Sum | 群集中的数据盘区总数。 更改此项指标可能会更改大规模数据的结构并在群集上施加较高的负载，因为合并数据盘区是 CPU 密集型活动。 | 无 |
| | | | |

### <a name="export-health-and-performance-metrics"></a>导出运行状况和性能指标

导出运行状况和性能指标跟踪导出操作的一般运行状况与性能，例如滞后、结果、记录数和利用率。

**指标** | **单位** | **聚集** | **度量值说明** | **尺寸** |
|---|---|---|---|---|
连续导出的记录数    | Count | SUM | 所有连续导出作业中的导出记录数。 | 无 |
连续导出最大延迟 |    Count   | Max   | 群集中连续导出作业报告的延迟（以分钟表示）。 | 无 |
连续导出挂起计数 | Count | Max   | 挂起的连续导出作业数。 这些作业已准备好运行，但在队列中等待，这可能是由于容量不足）。 
连续导出结果    | Count |   Count   | 每次连续导出运行的失败/成功结果。 | 连续导出名称 |
导出利用率 |    百分比 | Max   | 在群集的总导出容量（0 到 100 之间）中所使用的出口容量。 | 无 |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>引入运行状况和性能指标

引入运行状况和性能指标跟踪引入操作的一般运行状况与性能，例如延迟、结果和数量。

**指标** | **单位** | **聚集** | **度量值说明** | **尺寸** |
|---|---|---|---|---|
| 处理的事件数（适用于事件中心/IoT 中心） | Count | Max、Min、Sum | 从事件中心读取的以及由群集处理的事件总数 事件划分为群集引擎拒绝的事件和接受的事件。 | EventStatus |
| 引入延迟 | 秒 | Avg、Max、Min | 引入数据的延迟，根据从群集中收到数据，到数据可供查询的时间来测得。 引入延迟周期决于引入方案。 | 无 |
| 引入结果 | Count | Count | 失败和成功的引入操作总数。 使用“应用拆分”可以创建成功和失败结果桶，并分析维度（**值** > **状态**）。****| IngestionResultDetails |
| 引入量 (MB) | Count | Max、Sum | 引入到群集中的数据在压缩前的总大小 (MB)。 | 数据库 |
| | | | |  

### <a name="query-performance"></a>查询性能

查询性能指标跟踪查询持续时间，以及并发或受限制查询的总数。

**指标** | **单位** | **聚集** | **度量值说明** | **尺寸** |
|---|---|---|---|---|
| 查询持续时间 | 毫秒 | Avg、Min、Max、Sum | 收到查询结果之前所花费的总时间（不包括网络延迟）。 | QueryStatus |
| 并发查询总数 | Count | Avg、Max、Min、Sum | 群集中并行运行的查询数。 使用此指标可以很好地评估群集上的负载。 | 无 |
| 受限制的查询总数 | Count | Avg、Max、Min、Sum | 群集中受限制（被拒绝）的查询数。 允许的最大并发（并行）查询数在并发查询策略中定义。 | 无 |
| | | | |

### <a name="streaming-ingest-metrics"></a>流引入指标

流引入指标跟踪流引入数据和请求速率、持续时间与结果。

**指标** | **单位** | **聚集** | **度量值说明** | **尺寸** |
|---|---|---|---|---|
流引入数据速率 |    Count   | RateRequestsPerSecond | 引入群集的数据总量。 | 无 |
流引入持续时间   | 毫秒  | Avg、Max、Min | 所有流引入请求的总持续时间。 | 无 |
流引入请求速率   | Count | Count、Avg、Max、Min、Sum | 流引入请求总数。 | 无 |
流引入结果 | Count | Avg   | 流引入请求总数，按结果类型列出。 | 结果 |
| | | | |

有关[受支持的 Azure 数据资源管理器群集指标](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他信息。


## <a name="next-steps"></a>后续步骤

* [教程：在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code)
* [使用诊断日志监视 Azure 数据资源管理器引入操作](/azure/data-explorer/using-diagnostic-logs)
* [快速入门：在 Azure 数据资源管理器中查询数据](web-query-data.md)
