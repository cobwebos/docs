---
title: 监视 Azure 数据资源管理器性能、 运行状况和使用情况指标
description: 了解如何使用 Azure 数据资源管理器指标来监视群集的性能、 运行状况和使用情况。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: 5252ca8898439b63a8819f6abfd634de0786932b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851699"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>监视 Azure 数据资源管理器性能、 运行状况和使用情况指标

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 Azure 数据资源管理器指标提供有关运行状况和性能的群集资源的关键指标。 使用中详细介绍了这篇文章来监视 Azure 数据资源管理器群集运行状况和性能在特定方案中作为独立的指标的指标。 此外可以使用度量值用作的基础操作[Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)并[Azure 警报](/azure/azure-monitor/platform/alerts-metric-overview)。

## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，创建[Azure 免费帐户](https://azure.microsoft.com/free/)。

* 创建[群集和数据库](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="using-metrics"></a>使用指标

在 Azure 数据资源管理器群集中，选择**指标**以打开指标窗格并开始在群集上的分析。

![选择“指标”](media/using-metrics/select-metrics.png)

在指标窗格中：

![度量值窗格](media/using-metrics/metrics-pane.png)

1. 若要创建指标图表，请选择**指标**名称和相关**聚合**每个指标作为详细的下面。 **资源**并**指标 Namespace**选取器会预先选择为你的 Azure 数据资源管理器群集。

    **指标** | **单位** | **聚合** | **度量值说明**
    |---|---|---|---|
    | 缓存使用率 | 百分比 | Avg、 Max、 Min | （根据定义的缓存策略） 所需的缓存大小和群集 （提供的用户活动的 SSD 的总大小） 的总缓存大小之间的比率。 小于或等于 80%的平均缓存使用率是可承受群集的状态。 如果平均缓存使用率高于 80%时，群集应[纵向](manage-cluster-scale-up.md)到存储优化定价层或[向外扩展](manage-cluster-scale-out.md)到更多实例。 或者，调整缓存策略 （在缓存中的较少天）。 如果缓存利用率超过 100%，缓存的缓存策略，根据数据大小较大的缓存群集上的总大小。 |
    | CPU | 百分比 | Avg、 Max、 Min | 总 CPU 使用和在整个群集上可用的 CPU 之间的比率。 群集可承受平均 CPU 时间的 80%或更少。 CPU 的最大值为 100%，这意味着没有额外的计算资源来处理数据。 当群集不运行良好时，检查以确定是否会被阻止的特定 Cpu 的 CPU 的最大值。 |
    | （适用于事件中心） 处理的事件 | Count | Max、 Min、 Sum | 事件总数由事件中心发送和接收的群集。 事件被拆分为拒绝的事件和事件接受由群集引擎。 |
    | 引入延迟 | 秒 | Avg、 Max、 Min | 准备用于查询之前在群集中接收该数据的时间从引入的数据滞后时间。 以秒为单位引入延迟。 引入延迟期取决于引入方案。 |
    | 引入结果 | Count | Count | 引入操作的失败和成功的总数。 使用**应用拆分**创建存储桶的成功和失败的结果。|
    | 引入利用率 | 百分比 | Avg、 Max、 Min | 使用以引入数据的实际资源和容量策略执行引入中分配的总资源之间的比率。 默认容量策略不是能超过 512 引入并发操作或 75%的投资引入中的群集资源。 平均引入使用率小于或等于 80%的是群集的可承受状态。 引入使用率的最大值为 100%，这意味着使用所有群集引入功能并且可能会导致引入队列。 |
    | 引入量 （以 mb 为单位） | Count | Max、 Min、 Sum | 数据引入到 （以 mb 为单位） 群集的总大小。 单位为 Mb 的引入数据压缩前的数。 |
    | 保持处于活动状态 | Count | 平均 | 跟踪群集的响应能力。 完全能够做出响应的群集返回值 1 和受阻或断开连接的群集，则返回 0。 |
    | 查询持续时间 | 秒 | Count、 Avg、 Min、 Max、 Sum | 直到接收查询结果的总时间。 |
    | | | |

    其他信息有关[支持的 Azure 数据资源管理器群集指标](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. 选择**添加的度量值**按钮以查看同一个图表中绘制多个指标。
3. 选择 **+ 新图表**按钮以查看在一个视图中的多个图表。
4. 使用时间选取器来更改时间范围 (默认： 过去 24 小时)。
5. 使用[**将筛选器添加**并**应用拆分**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting)对于具有维度的指标。
6. 选择**固定到仪表板**将图表配置添加到仪表板，以便可以再次查看它。
7. 设置**新的警报规则**直观显示您使用组条件的指标。 新的警报规则将包括图表的目标资源、指标、拆分和筛选器维度。 中修改这些设置[警报规则创建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)。

使用的其他信息[指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：在 Azure 数据资源管理器中查询数据](web-query-data.md)
