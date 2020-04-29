---
title: 监视 Azure Cosmos DB 中操作的吞吐量使用情况
description: 了解如何在 Azure Cosmos DB 中监视操作的吞吐量或请求单元使用量。 Azure Cosmos DB 帐户的所有者可以了解哪些操作需要更多请求单位。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115425"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何监视 Azure Cosmos DB 中操作的吞吐量或请求单元使用情况

Azure Cosmos DB 的 Azure Monitor 提供用于监视帐户和创建仪表板的指标视图。 默认情况下会收集 Azure Cosmos DB 度量值，此功能不要求你显式启用或配置任何内容。 "**请求单位总数**" 指标用于获取不同类型操作的请求单位使用量。 稍后，你可以分析哪些操作使用了大多数吞吐量。 默认情况下，吞吐量数据按一分钟的时间间隔进行聚合。 不过，您可以通过更改时间粒度选项来更改聚合单位。

可以通过两种方法来分析请求单位使用情况数据：

* 在给定的时间间隔内，哪些操作需要更多请求单位。
* 通常，哪些操作会消耗更多的请求单位，使工作负荷占主导地位。
此分析使你可以专注于诸如 insert、upsert 等操作，并查看其索引。 你可以找出是否正在索引特定字段，以及如何修改[索引策略](index-policy.md#include-exclude-paths)来包括或排除这些路径。

如果你注意到某些查询需要更多请求单位，则可以执行以下操作：

* 请考虑是否请求正确的数据量。
* 修改查询以使用带有筛选子句的索引。
* 执行更便宜的 UDF 函数调用。
* 定义分区键，以最大程度地减少在不同分区中查询的风扇。
* 你还可以使用调用响应中返回的查询度量值、诊断日志详细信息并参阅[查询性能优化](sql-api-query-metrics.md)一文，了解有关查询执行的详细信息。
* 可以从 sum 开始，然后使用正确的维度查看平均利用率。

## <a name="view-the-total-request-unit-usage-metric"></a>查看请求单位总使用量指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从左侧导航栏中选择 "**监视器**"，然后选择 "**指标**"。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。**************** 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。************

   ![选择 Azure Cosmos DB 帐户以查看指标](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 接下来，从可用指标列表中选择 "**请求单位总数**"。 若要详细了解此列表中的所有可用指标，请参阅[度量值（按类别](monitor-cosmos-db-reference.md)）一文。 在此示例中，我们选择 "**请求单位总数**" 和 "**平均值**" 作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。******** 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择指标](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>请求单位使用情况的筛选器

还可以筛选指标，并获取按特定的**CollectionName**、 **DatabaseName**、 **OperationType**、 **Region**、 **Status**和**StatusCode**显示的图表。 "**添加筛选器**" 和 "**应用拆分**" 选项允许你筛选请求单位使用情况并对指标进行分组。

若要按总计（总和）或平均值获取每个操作的请求单位使用情况，请选择 "**应用拆分**" 并选择 "**操作类型**" 和 "筛选器" 值，如下图所示：

   ![Cosmos DB Azure monitor 中的操作的请求单位](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

如果要查看按集合划分的请求单位使用情况，请选择 "**应用拆分**" 并选择集合名称作为筛选器。 你将在仪表板中看到类似于以下的聊天，其中包含一系列集合。 然后，可以选择特定的集合名称来查看更多详细信息：

   ![Azure monitor 中的集合为所有操作 Cosmos DB 请求单位](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)