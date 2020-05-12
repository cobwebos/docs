---
title: 监视 Azure Cosmos DB 中的操作的吞吐量用量
description: 了解如何监视 Azure Cosmos DB 中的操作的吞吐量或请求单位用量。 Azure Cosmos DB 帐户的所有者可以了解哪些操作需要更多请求单位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: 4ea195dfbf9eddbf10680196c9c75646b45380ae
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119007"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何监视 Azure Cosmos DB 中的操作的吞吐量或请求单位用量

适用于 Azure Cosmos DB 的 Azure Monitor 提供一个指标视图，用于监视帐户和创建仪表板。 默认情况下，系统会收集 Azure Cosmos DB 指标，此功能不需要你显式启用或配置任何设置。 “请求单位总数”指标用于获取不同类型的操作的请求单位用量  。 以后，你可以分析哪些操作使用了大部分吞吐量。 默认情况下，吞吐量数据是按一分钟间隔聚合的。 但是，可以通过更改时间粒度选项来更改聚合单位。

可通过两种方式分析请求单位用量数据：

* 在给定时间间隔内，哪些操作占用了较多的请求单位。
* 一般而言，哪些操作通过消耗较多的请求单位支配了工作负荷。
这种分析使你可以专注于插入、更新插入等操作并查看其索引编制。 可以查明特定字段的索引编制是否过度/不足，并修改[索引策略](index-policy.md#include-exclude-paths)以包含或排除路径。

如果发现某些查询占用了较多的请求单位，可以采取如下所述的措施：

* 重新考虑请求的数据量是否适当。
* 修改查询以使用带有筛选子句的索引。
* 执行开销更低的 UDF 函数调用。
* 定义分区键，以便最大程度地减少将查询扇出到不同的分区中的情况。
* 还可以使用调用响应中返回的查询指标以及诊断日志详细信息，并参考[查询性能优化](sql-api-query-metrics.md)一文来详细了解查询执行。
* 可以从总和开始，然后使用适当的维度查看平均利用率。

## <a name="view-the-total-request-unit-usage-metric"></a>查看请求单位用量指标总计

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中选择“监视”，然后选择“指标”。  

   ![Azure Monitor 中的“指标”窗格](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。     对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。   

   ![选择 Azure Cosmos DB 帐户以查看指标](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 接下来，从可用指标的列表中选择“请求单位总数”指标。  若要详细了解此列表中的所有可用指标，请参阅[按类别划分的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，让我们选择“请求单位总数”和“平均”作为聚合值。   除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。   可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择指标](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>请求单位用量筛选器

还可以按特定的 CollectionName、DatabaseName、OperationType、Region、Status 和 StatusCode 筛选指标并显示相应的图表。       使用“添加筛选器”和“应用拆分”选项可以筛选请求单位用量并将指标分组。  

若要按总计（总和）或平均值获取每个操作的请求单位用量，请选择“应用拆分”，然后选择“操作类型”和筛选器值，如下图所示：  

   ![Azure Monitor 中的操作的 Cosmos DB 请求单位](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

若要按集合查看请求单位用量，请选择“应用拆分”并选择集合名称作为筛选器。  在仪表板中将会看到如下所示的图表，其中包含所选的集合。 然后，可以选择特定的集合名称以查看更多详细信息：

   ![Azure Monitor 中按集合列出的所有操作的 Cosmos DB 请求单位](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)