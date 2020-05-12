---
title: 监视 Azure Cosmos 容器或帐户的规范化 RU/秒
description: 了解如何在 Azure Cosmos DB 中监视操作的规范化请求单位使用情况。 Azure Cosmos DB 帐户的所有者可以了解哪些操作消耗更多请求单位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118823"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>如何监视 Azure Cosmos 容器或帐户的规范化 RU/秒

适用于 Azure Cosmos DB 的 Azure Monitor 提供一个指标视图，用于监视帐户和创建仪表板。 默认情况下，系统会收集 Azure Cosmos DB 指标，此功能不需要你显式启用或配置任何设置。

**规范化 RU 消耗**指标用于查看副本是否 wrt 到分区键范围内的请求单位消耗。 Azure Cosmos DB 在所有物理分区之间平均分配吞吐量。 此指标提供副本集内最大吞吐量使用率的每秒视图。 使用此度量值时，如果看到的请求单位利用率很高，则应增加吞吐量以满足工作负荷的需求。

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>规范化 RU/s 更高时的预期和操作

当标准化的 RU/s 消耗达到100% 时，客户端会收到速率限制错误。 客户端应遵循等待时间，然后重试。 如果存在达到100% 利用率的短峰值，则表示副本上的吞吐量达到其最大性能限制。 例如，单个操作（如使用副本上的所有 RU/s 的存储过程）将导致较短的 RU/秒消耗。 在这种情况下，如果请求速率较低，则不会有任何即时速率限制错误。 这是因为，Azure Cosmos DB 允许请求对特定请求进行预配的 RU/秒，而在该时间段内的其他请求需要支付速率限制。

Azure Monitor 度量值可帮助你使用**Total Requests**度量查找每个状态代码的操作。 稍后，可以通过429状态代码对这些请求进行筛选并按**操作类型**对其进行拆分。

若要查找速率受限的请求，建议使用诊断日志获取此信息。

如果连续峰值为100%，则会消耗 RU/s 或接近100%，建议增加吞吐量。 可以通过使用 Azure monitor 指标和 Azure monitor 日志来找出哪些操作是繁重的，以及使用高峰。

**规范化 RU 消耗**指标还用于查看哪些分区键范围在使用方面更热;这样，便可以在分区键范围内偏移吞吐量。 稍后可以跟进，查看 Azure Monitor 日志中的**PartitionKeyRUConsumption**日志，以获取有关使用情况中哪些逻辑分区键处于热状态的信息。

## <a name="view-the-normalized-request-unit-consumption-metric"></a>查看规范化请求单位消耗指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 在左侧导航栏中选择“监视”，然后选择“指标”。  

   ![Azure Monitor 中的“指标”窗格](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。     对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。   

   ![选择 Azure Cosmos 帐户以查看指标](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 等的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别划分的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，我们选择 "**标准化 RU 消耗**度量值" 和 "**最大**值" 作为聚合值。

   除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。   可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。

   ![从 Azure 门户中选择指标](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>规范化请求单位消耗的筛选器

你还可以筛选度量值和由特定的**CollectionName**、 **DatabaseName**、 **PartitionKeyRangeID**和**区域**显示的图表。 若要筛选度量值，请选择 "**添加筛选器**" 并选择所需的属性，如**CollectionName**和相应的值。 然后，关系图显示所选时间段的容器使用的规范化 RU 消耗单位。  

可以使用“应用拆分”选项将指标分组。   

每个容器的规范化请求单位消耗指标显示如下图所示：

![将筛选器应用于规范化请求单位消耗指标](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)