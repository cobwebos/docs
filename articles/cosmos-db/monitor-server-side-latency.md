---
title: 如何监视 Azure Cosmos DB 中操作的服务器端延迟
description: 了解如何监视 Azure Cosmos DB 帐户或容器中的操作的服务器延迟。 Azure Cosmos DB 帐户的所有者可了解 Azure Cosmos 帐户的服务器端延迟问题。
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 9c266e42804a12403e446bf024e93fe879497570
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803256"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何监视 Azure Cosmos DB 容器或帐户中的操作的服务器端延迟

适用于 Azure Cosmos DB 的 Azure Monitor 提供一个指标视图，用于监视帐户和创建仪表板。 默认情况下，系统会收集 Azure Cosmos DB 指标，此功能不需要你显式启用或配置任何设置。 服务器端延迟指标用于查看操作的服务器端延迟。 Azure Cosmos DB 为直接连接的点读取/写入操作提供小于 10 ms 的 SLA。 对于点读和写操作，sla 按照 [sla 文档](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)中的详细信息进行计算。

如果你看到点操作的异常长延迟（例如：

* 在直接连接模式下具有分区键和 ID 的 GET 或 SET 操作
* 读取或写入操作，或
* 查询

您可以查找诊断日志以查看返回的数据的大小。 如果你看到查询操作的持续时间较长，则应查找更高吞吐量或所使用的 [RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) 的诊断日志。 服务器端延迟显示在将数据返回到客户端之前后端基础结构花费的时间量。 请务必查看此指标，排除所有后端延迟问题。

## <a name="view-the-server-side-latency-metric"></a>查看服务器端延迟指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中选择“监视”，然后选择“指标”。 

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格":::

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。    对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。  
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Azure Monitor 中的“指标”窗格":::

1. 接下来，从可用指标列表中选择 **服务器端延迟**  指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别划分的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，让我们选择 **服务器端延迟** ，并选择 **Avg** 作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。  可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 您可以查看所选时间段内每分钟的服务器端延迟。  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Azure Monitor 中的“指标”窗格":::

## <a name="filters-for-server-side-latency"></a>服务器端延迟的筛选器

你还可以筛选指标，并获取由特定的 **CollectionName**、 **ConnectionMode**、 **DatabaseName**、 **OperationType**、 **Region**和 **PublicAPIType**显示的图表。 

若要筛选度量值，请选择 " **添加筛选器** " 并选择所需属性（如 **PublicAPIType** ），并选择值 **sql**。 为 **OperationType**添加另一个筛选器。 然后，关系图显示所选时间段内不同操作的服务器端延迟。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

每个操作的 **服务器端延迟** 指标显示如下图所示：

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Azure Monitor 中的“指标”窗格" 选项对度量值进行分组。  

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)
