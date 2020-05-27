---
title: 监视 Azure Cosmos 容器或帐户的规范化 RU/秒
description: 了解如何在 Azure Cosmos DB 中监视某个操作的规范化请求单位使用量。 Azure Cosmos DB 帐户的所有者可以了解哪些操作消耗了较多的请求单位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118823"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何监视 Azure Cosmos 容器或帐户的规范化 RU/秒

适用于 Azure Cosmos DB 的 Azure Monitor 提供了用来监视帐户和创建仪表板的指标视图。 Azure Cosmos DB 指标是默认收集的，此功能不需要你显式启用或配置任何项。

**规范化 RU 消耗**指标用于查看副本是否能够满足各个分区键范围的请求单位消耗。 Azure Cosmos DB 在所有物理分区之间平均分配吞吐量。 此指标提供某个副本集内最大吞吐量利用率的每秒视图。 使用此指标，如果你看到请求单位利用率很高，则应该增大吞吐量以满足工作负荷的需要。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>规范化 RU/秒较高时会发生的事情和应该做的事情

当规范化 RU/秒消耗达到 100% 时，客户端会收到速率限制错误。 客户端应等待一段时间，然后重试。 如果存在达到 100% 利用率的短期峰值，则意味着副本上的吞吐量已达到其最大性能限制。 例如，如果某个操作（例如存储过程）消耗了副本上的所有吞吐量（RU/秒），则会导致规范化 RU/秒消耗出现短期峰值。 在这种情况下，如果请求速率较低，则不会立即出现速率限制错误。 这是因为，Azure Cosmos DB 允许特定请求的请求速率超过预配的 RU/秒，但会对该时间段内的其他请求进行速率限制。

有了 Azure Monitor 指标，就可以使用**总请求数**指标来查找每个状态代码的操作。 稍后，你可以通过 429 状态代码对这些请求进行筛选，并按**操作类型**来划分这些请求。

若要查找速率受限的请求，建议使用诊断日志获取此信息。

如果规范化 RU/秒消耗持续达到 100% 峰值或接近 100%，建议增大吞吐量。 可以使用 Azure Monitor 指标和 Azure Monitor 日志查明哪些操作繁忙及其峰值使用量是多少。

**规范化 RU 消耗**指标还用来查看哪个分区键范围的使用量较大，从而使你可以将吞吐量向某个分区键范围倾斜。 你稍后可以跟进，查看 Azure Monitor 日志中的 **PartitionKeyRUConsumption** 日志，了解哪些逻辑分区键使用量较大。

## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看规范化请求单位消耗指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 从左侧导航栏中选择“监视器”，然后选择“指标”。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. 从“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。  对于“资源类型”，请选择“Azure Cosmos DB 帐户”，接着选择你的现有 Azure Cosmos 帐户之一，然后选择“应用”。

   ![选择一个 Azure Cosmos 帐户来查看指标](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. 接下来，可以从可用指标的列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 和其他方面的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别列出的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，我们选择“规范化 RU 消耗”指标，并选择“最大值”作为聚合值。

   除了这些详细信息之外，还可以选择指标的时间范围和时间粒度。 最多可以查看过去 30 天的指标。  在你应用筛选器后，系统会根据筛选器显示一个图表。

   ![从 Azure 门户中选择一个指标](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>针对规范化请求单位消耗的筛选器

还可以按特定的 **CollectionName**、**DatabaseName**、**PartitionKeyRangeID** 和**区域**筛选所显示的指标和图表。 若要筛选指标，请选择“添加筛选器”，并选择所需的属性（例如 **CollectionName**）和所关注的相应值。 然后，图中会显示在所选时间段内针对该容器消耗的规范化 RU 消耗单位。  

可以使用“应用拆分”选项对指标进行分组。  

将会显示每个容器的规范化请求单位消耗指标，如下图所示：

![对规范化请求单位消耗指标应用筛选器](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的“诊断设置”[](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)