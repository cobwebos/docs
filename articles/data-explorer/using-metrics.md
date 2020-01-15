---
title: 使用指标监视 Azure 数据资源管理器性能、运行状况和使用情况
description: 了解如何使用 Azure 数据资源管理器度量值监视群集的性能、运行状况和使用情况。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: 1bc63ac4dc3930ad0423baa98453bd927c295a72
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945755"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用指标监视 Azure 数据资源管理器性能、运行状况和使用情况

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，可实时分析从应用程序、网站和 IoT 设备等流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 Azure 数据资源管理器度量值提供有关群集资源的运行状况和性能的关键指标。 使用本文中详述的指标来监视 Azure 数据资源管理器群集的运行状况和性能，具体方案为独立的指标。 你还可以使用度量值作为运营[Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)和[Azure 警报](/azure/azure-monitor/platform/alerts-metric-overview)的基础。

## <a name="prerequisites"></a>必备组件

* 如果没有 Azure 订阅，请创建一个免费的[azure 帐户](https://azure.microsoft.com/free/)。

* 创建[群集和数据库](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="using-metrics"></a>使用指标

在 Azure 数据资源管理器群集中，选择 "**指标**" 以打开 "度量值" 窗格并在群集上开始分析。

![选择“指标”](media/using-metrics/select-metrics.png)

在 "度量值" 窗格中：

![指标窗格](media/using-metrics/metrics-pane.png)

1. 若要创建指标图表，请按如下所述为每个指标选择**指标**名称和相关**聚合**。 **资源**和**指标命名空间**选取器已预先选择 Azure 数据资源管理器群集。

    **指标** | **单位** | **聚合** | **指标描述**
    |---|---|---|---|
    | 缓存利用率 | 百分比 | Avg、Max、Min | 群集当前正在使用的已分配缓存资源的百分比。 缓存是根据定义的缓存策略为用户活动分配的 SSD 大小。 对于群集，平均缓存利用率为80% 或更低。 如果平均缓存利用率高于80%，则应将群集[扩展](manage-cluster-vertical-scaling.md)到存储优化定价层，或[扩大](manage-cluster-horizontal-scaling.md)到更多实例。 或者，改编缓存策略（缓存中的天数更少）。 如果缓存利用率超过100%，则根据缓存策略缓存的数据大小越大，群集上的缓存的总大小就越大。 |
    | CPU | 百分比 | Avg、Max、Min | 群集中的计算机当前正在使用的已分配计算资源的百分比。 对于群集，平均 CPU 为80% 或更低。 CPU 的最大值为100%，这意味着没有额外的计算资源可用于处理数据。 当群集不能正常运行时，请检查 CPU 的最大值，以确定是否有特定的 Cpu 被阻止。 |
    | 处理的事件（事件中心） | 计数 | Max、Min、Sum | 从事件中心读取并由群集处理的事件总数。 事件被拆分为已拒绝的事件和群集引擎接受的事件。 |
    | 引入延迟 | 秒数 | Avg、Max、Min | 数据引入的延迟时间，从在群集中接收数据到查询准备就绪。 引入延迟时间取决于引入方案。 |
    | 引入结果 | 计数 | 计数 | 失败和成功的引入操作的总数。 使用 "**应用拆分**" 创建成功和失败结果的存储桶，并分析维度（**值** > **状态**）。|
    | 引入利用率 | 百分比 | Avg、Max、Min | 用于从分配的资源（容量策略）中引入数据以执行引入的实际资源的百分比。 默认容量策略不超过512个并发引入操作或引入中投入的群集资源的75%。 80% 或更低的平均引入利用率是群集的可持续状态。 引入使用率的最大值为100%，这意味着使用所有群集引入能力，并且可能会导致引入队列。 |
    | 引入卷（MB） | 计数 | Max、Min、Sum | 压缩前引入到群集的数据的总大小（以 MB 为单位）。 |
    | 保持活动状态 | 计数 | Avg | 跟踪群集的响应能力。 完全响应的群集返回值1，阻止或断开连接的群集返回0。 |
    | 查询持续时间 | 秒数 | Count、Avg、Min、Max、Sum | 接收到查询结果之前的总时间（不包括网络延迟）。 |
    | 并发查询总数 | 计数 | Avg、Max、Min、Sum | 群集中并行运行的查询数。 此指标是估计群集上的负载的好方法。 |
    | 限制查询总数 | 计数 | Avg、Max、Min、Sum | 群集中限制（拒绝）的查询数。 并发查询策略中定义了允许的最大并发（并行）查询数。 |
    | 阻止的命令总数 | 计数 | Avg、Max、Min、Sum | 由于已达到允许的最大并发（并行）命令数，导致群集中的限制（拒绝）命令数。 |
    | 总区数 | 计数 | Avg、Max、Min、Sum | 群集中的数据区总数。 此指标的变化可能意味着大规模的数据结构更改和群集上的高负载，因为合并数据区是一个 CPU 繁重的活动。 |
    | | | | |

    有关[受支持的 Azure 数据资源管理器群集指标](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他信息

2. 选择 "**添加度量值**" 按钮可查看在同一图表中绘制多个度量值。
3. 选择 " **+ 新建图表**" 按钮，在一个视图中查看多个图表。
4. 使用时间选取器更改时间范围（默认值：过去24小时）。
5. 对于具有维度的指标，使用 " [**添加筛选器**" 和 "**应用拆分**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) "。
6. 选择 "**固定到仪表板**"，将图表配置添加到仪表板，以便可以再次查看。
7. 设置**新的警报规则**，以使用设置的条件可视化指标。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 在[警报规则创建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改这些设置。

有关使用[指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)的其他信息。


## <a name="next-steps"></a>后续步骤

* [教程：在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code)
* [使用诊断日志监视 Azure 数据资源管理器引入操作](/azure/data-explorer/using-diagnostic-logs)
* [快速入门：在 Azure 数据资源管理器中查询数据](web-query-data.md)
