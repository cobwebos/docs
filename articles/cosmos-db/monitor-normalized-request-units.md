---
title: 监视 Azure Cosmos 容器或帐户的规范化 RU/秒
description: 了解如何在 Azure Cosmos DB 中监视某个操作的规范化请求单位使用量。 Azure Cosmos DB 帐户的所有者可以了解哪些操作消耗了较多的请求单位。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 183b161039b86ce824fd0bfde82cf291d54024fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801471"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何监视 Azure Cosmos 容器或帐户的规范化 RU/秒

适用于 Azure Cosmos DB 的 Azure Monitor 提供了用来监视帐户和创建仪表板的指标视图。 Azure Cosmos DB 指标是默认收集的，此功能不需要你显式启用或配置任何项。

**规范化 RU 消耗**指标用于查看分区键范围相对于流量的饱和程度。 Azure Cosmos DB 在所有分区键范围内平均分配吞吐量。 此指标提供分区键范围的最大吞吐量使用率的每秒视图。 使用此度量值可以计算给定容器的分区键范围内的 RU/秒使用量。 使用此度量值时，如果在 Azure monitor 中的所有分区键范围内看到的请求单位利用率很高，则应增加吞吐量以满足工作负荷的需求。 示例-标准化利用率定义为所有分区键范围内的 RU/s 利用率的最大值。 例如，假设最大吞吐量为 20000 RU/s，并且有两个分区键范围（P_1 和 P_2），每个分区键都可以缩放到 10000 RU/秒。 在给定的秒数内，如果 P_1 已使用 6000 RU，而 P_2 已使用 8000 RU，则标准化利用率为 MAX(6000 RU / 10,000 RU, 8000 RU / 10,000 RU) = 0.8。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>规范化 RU/秒较高时会发生的事情和应该做的事情

当标准化的 RU/s 消耗在给定的分区键范围内达到100% 时，如果客户端仍在该时间窗口中向该特定分区键范围发出请求，则会收到速率限制错误。 客户端应遵循建议的等待时间，并重试请求。 SDK 使你可以通过相应的等待重试预配置时间轻松地处理这种情况。  这并不是必需的，因为规范化的 RU 已达到100%。 这是因为，规范化 RU 是一个表示所有分区键范围的最大使用量的单个值，而另一个分区键范围可能正忙，但其他分区键范围可为这些请求提供服务。 例如，单个操作（如使用分区键范围内的所有 RU/s 的存储过程）将导致规范化的 RU/s 消耗中出现短暂峰值。 在这种情况下，如果请求速率较低或对不同分区键范围内的其他分区发出请求，则不会有任何即时速率限制错误。 

Azure Monitor 指标通过使用 **Total Requests** 指标帮助你查找 SQL API 的每个状态代码的操作。 稍后，你可以通过 429 状态代码对这些请求进行筛选，并按**操作类型**来划分这些请求。  

若要查找速率受限的请求，建议使用诊断日志获取此信息。

如果连续峰值为100%，则在多个分区键范围内进行规范化的 RU/s 消耗或接近100%，则建议增加吞吐量。 可以通过使用 Azure monitor 指标和 Azure monitor 诊断日志来找出哪些操作非常繁重，使用高峰。

总的来说， **规范化 RU 消耗** 指标用于查看在使用情况下哪些分区键范围更热。 这样，便可以在分区键范围内进行吞吐量的偏差。 你稍后可以跟进，查看 Azure Monitor 日志中的 **PartitionKeyRUConsumption** 日志，了解哪些逻辑分区键使用量较大。 这将指向分区键选择中的更改或应用程序逻辑中的更改。 若要解决速率限制问题，请在多个分区上分布数据负载，或者只是实际需要增加吞吐量。 



## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看规范化请求单位消耗指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 在左侧导航栏中选择“监视”，然后选择“指标”。 

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格":::

3. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。    对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。  

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Azure Monitor 中的“指标”窗格":::

4. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 和其他方面的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别列出的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，我们选择“规范化 RU 消耗”指标，并选择“最大值”作为聚合值。

   除了这些详细信息之外，还可以选择指标的时间范围和时间粒度。 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Azure Monitor 中的“指标”窗格":::

### <a name="filters-for-normalized-request-unit-consumption"></a>针对规范化请求单位消耗的筛选器

还可以按特定的 **CollectionName**、**DatabaseName**、**PartitionKeyRangeID** 和**区域**筛选所显示的指标和图表。 若要筛选指标，请选择“添加筛选器”，并选择所需的属性（例如 **CollectionName**）和所关注的相应值。 然后，图中会显示在所选时间段内针对该容器消耗的规范化 RU 消耗单位。  

可以使用“应用拆分”选项对指标进行分组。  

将会显示每个容器的规范化请求单位消耗指标，如下图所示：

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Azure Monitor 中的“指标”窗格":::

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)
