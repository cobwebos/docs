---
title: 监视 Azure Cosmos DB 中操作的吞吐量使用情况
description: 了解如何监视 Azure Cosmos DB 中操作的吞吐量或请求单位使用情况。 Azure Cosmos DB 帐户的所有者可以了解哪些操作占用了更多请求单元。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115425"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>如何监视 Azure Cosmos DB 中操作的吞吐量或请求单位使用情况

Azure Cosmos DB 的 Azure 监视器提供了一个指标视图，用于监视您的帐户并创建仪表板。 默认情况下，Azure Cosmos DB 指标是收集的，此功能不需要您显式启用或配置任何内容。 **总请求单位**指标用于获取不同类型的操作的请求单位使用情况。 稍后，您可以分析哪些操作使用了大部分吞吐量。 默认情况下，吞吐量数据以一分钟间隔聚合。 但是，您可以通过更改时间粒度选项来更改聚合单元。

有两种方法可以分析请求单位使用情况数据：

* 在给定的时间间隔内，哪些操作占用了更多的请求单位。
* 哪些操作通常通过消耗更多请求单位来主导您的工作负载。
此分析允许您专注于插入、向上计算等操作，并查看其索引。 您可以找出是否过度/低于索引特定字段，并修改[索引策略](index-policy.md#include-exclude-paths)以包括或排除路径。

如果您注意到某些查询正在获取更多请求单位，则可以采取如下操作：

* 如果您请求了适当数量的数据，请重新考虑。
* 修改查询以使用索引与筛选器子句。
* 执行成本较低的 UDF 函数调用。
* 定义分区键，以尽量减少风扇从查询到不同的分区。
* 您还可以使用呼叫响应中返回的查询指标、诊断日志详细信息，并参考[查询性能调优](sql-api-query-metrics.md)一文来了解有关查询执行的详细信息。
* 可以从总和开始，然后使用正确的维度查看平均利用率。

## <a name="view-the-total-request-unit-usage-metric"></a>查看总请求单位使用情况指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从左侧导航栏中选择 **"监视器"，** 然后选择 **"指标**"。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。**************** 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。************

   ![选择 Azure 宇宙数据库帐户以查看指标](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 接下来，从可用指标列表中选择**总请求单位**指标。 要详细了解此列表中的所有可用指标，请参阅[按类别的指标](monitor-cosmos-db-reference.md)。 在此示例中，让我们选择**总请求单位**，选择**平均值**作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。******** 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择指标](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>请求单位使用情况的筛选器

您还可以筛选指标，并获取由特定**集合名称**、**数据库名称**、**操作类型**、**区域**、**状态**和**状态代码**显示的图表。 "**添加"筛选器**和 **"应用拆分"** 选项允许您筛选请求单位使用情况并分组指标。

要按总计（总和）或平均值获取每个操作的请求单位使用情况，请选择 **"应用拆分**"并选择**操作类型**和筛选器值，如下图所示：

   ![宇宙 DB 请求单位 以在 Azure 监视器中操作](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

如果要按集合查看请求单位使用情况，请选择 **"应用拆分**"，然后选择集合名称作为筛选器。 您将看到如下所示的聊天，并在仪表板中选择集合。 然后，您可以选择特定的集合名称以查看更多详细信息：

   ![宇宙 DB 请求 Azure 监视器中集合的所有操作的单位](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure 宇宙数据库数据。
* [审核 Azure 宇宙 DB 控制平面操作](audit-control-plane-logs.md)