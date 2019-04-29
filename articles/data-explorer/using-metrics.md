---
title: 使用指标监视 Azure 数据资源管理器的性能、运行状况和使用情况
description: 了解如何使用 Azure 数据资源管理器指标来监视群集的性能、运行状况和使用情况。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826762"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用指标监视 Azure 数据资源管理器的性能、运行状况和使用情况

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 Azure 数据资源管理器指标提供群集资源运行状况和性能的关键指标。 使用本文中详述的独立指标可以监视特定方案中 Azure 数据资源管理器群集的运行状况和性能。 还可以将指标用作正常运行的 [Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)和 [Azure 警报](/azure/azure-monitor/platform/alerts-metric-overview)的基础。

## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，创建[Azure 免费帐户](https://azure.microsoft.com/free/)。

* 创建[群集和数据库](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="using-metrics"></a>使用指标

在 Azure 数据资源管理器群集中，选择“指标”打开指标窗格，开始对群集进行分析。

![选择“指标”](media/using-metrics/select-metrics.png)

在“指标”窗格中：

![“指标”窗格](media/using-metrics/metrics-pane.png)

1. 若要创建指标图表，请根据下面的详述，选择**指标**名称和每个指标的相关**聚合**。 系统为 Azure 数据资源管理器群集预先选择了“资源”和“指标命名空间”选取器。

    **指标** | **单位** | **聚合** | **指标说明**
    |---|---|---|---|
    | 缓存利用率 | 百分比 | Avg、Max、Min | 群集当前使用的已分配缓存资源百分比。 缓存是指为用户活动分配的、符合定义的缓存策略的 SSD 大小。 80% 或更低的平均缓存利用率可以维持群集的正常状态。 如果平均缓存利用率超过 80%时，则群集应[纵向扩展](manage-cluster-scale-up.md)到存储优化的定价层级，或[横向扩展](manage-cluster-scale-out.md)为更多实例。 也可以调整缓存策略（减少缓存的天数）。 如果缓存利用率超过 100%，则根据缓存策略缓存的数据大小将大于群集上的缓存总大小。 |
    | CPU | 百分比 | Avg、Max、Min | 群集中的计算机当前使用的已分配计算资源百分比。 80% 或更低的平均 CPU 利用率可以维持群集的正常状态。 最大 CPU 利用率值为 100%，表示没有更多的计算资源可用于处理数据。 如果某个群集的性能不佳，请检查最大 CPU 利用率值，以确定特定的 CPU 是否阻塞。 |
    | 处理的事件数（针对事件中心） | Count | Max、Min、Sum | 从事件中心读取的以及由群集处理的事件总数 事件划分为群集引擎拒绝的事件和接受的事件。 |
    | 引入延迟 | 秒 | Avg、Max、Min | 引入数据的延迟，根据从群集中收到数据，到数据可供查询的时间来测得。 引入延迟周期决于引入方案。 |
    | 引入结果 | Count | Count | 失败和成功的引入操作总数。 使用“应用拆分”可以创建成功和失败结果桶，并分析维度（**值** > **状态**）。|
    | 引入利用率 | 百分比 | Avg、Max、Min | 用于从容量策略中分配的所有资源引入数据，以执行引入的实际资源百分比。 默认的容量策略是不超过 512 个并发的引入操作，或者不超过引入中投入的群集资源数的 75%。 80% 或更低的平均引入利用率可以维持群集的正常状态。 最大的引入利用率值为 100%，表示使用整个群集引入能力，这可能会生成引入队列。 |
    | 引入量 (MB) | Count | Max、Min、Sum | 引入到群集中的数据在压缩前的总大小 (MB)。 |
    | 保持活动状态 | Count | Avg | 跟踪群集的响应度。 完全可响应的群集将返回值 1，受阻或断开连接的群集将返回 0。 |
    | 查询持续时间 | 秒 | Count、Avg、Min、Max、Sum | 收到查询结果之前所花费的总时间（不包括网络延迟）。 |
    | | | |

    有关[支持的 Azure 数据资源管理器群集指标](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的更多信息

2. 选择“添加指标”按钮可以查看同一图表中绘制的多个指标。
3. 选择“+ 新建图表”按钮可在一个视图中查看多个图表。
4. 使用时间选取器更改时间范围（默认：过去 24 小时）。
5. 对包含维度的指标使用[**添加筛选器**和**应用拆分**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting)。
6. 选择“固定到仪表板”可将图表配置添加到仪表板，以便可以再次查看它。
7. 设置**新的警报规则**可以使用设置的条件将指标可视化。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 在[警报规则创建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改这些设置。

有关使用[指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)的更多信息。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：在 Azure 数据资源管理器中查询数据](web-query-data.md)
