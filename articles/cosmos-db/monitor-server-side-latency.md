---
title: 如何监视 Azure Cosmos DB 中操作的服务器端延迟
description: 了解如何监视 Azure Cosmos DB 帐户或容器中的操作的服务器延迟。 Azure Cosmos DB 帐户的所有者可了解 Azure Cosmos 帐户的服务器端延迟问题。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113926"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何监视 Azure Cosmos DB 容器或帐户中的操作的服务器端延迟

Azure Cosmos DB 的 Azure Monitor 提供用于监视帐户和创建仪表板的指标视图。 默认情况下会收集 Azure Cosmos DB 度量值，此功能不要求你显式启用或配置任何内容。 服务器端延迟指标用于查看操作的服务器端延迟。 Azure Cosmos DB 为直接连接的点读取/写入操作提供小于 10 ms 的 SLA。 对于点读和写操作，sla 按照[sla 文档](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)中的详细信息进行计算。

如果你发现点操作的延迟非常长，例如：

* 在直接模式下使用分区键和 ID 的 get 或 set 操作
* 读取或写入操作，或
* 查询

您可以查找诊断日志以查看返回的数据的大小。 如果你看到查询操作的持续时间较长，则可以查看诊断日志中所返回数据的大小、所用的[吞吐量或 RU/秒](cosmosdb-monitor-resource-logs.md#diagnostic-queries)，或在给定时间段内的此类操作的数量。 这样，便可以调试服务器端延迟问题。

## <a name="view-the-server-side-latency-metric"></a>查看服务器端延迟指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从左侧导航栏中选择 "**监视器**"，然后选择 "**指标**"。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。**************** 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。************
   
   ![选择 Azure Cosmos DB 帐户以查看指标](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. 接下来，从可用指标列表中选择**服务器端延迟**指标。 若要详细了解此列表中的所有可用指标，请参阅[度量值（按类别](monitor-cosmos-db-reference.md)）一文。 在此示例中，让我们选择**服务器端延迟**，并选择**Avg**作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。******** 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 您可以查看所选时间段内每分钟的服务器端延迟。  

   ![从 Azure 门户中选择服务器端延迟指标](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>服务器端延迟的筛选器

你还可以筛选指标，并获取由特定的**CollectionName**、 **ConnectionMode**、 **DatabaseName**、 **OperationType**、 **Region**和**PublicAPIType**显示的图表。 

若要筛选度量值，请选择 "**添加筛选器**" 并选择所需属性（如**PublicAPIType** ），并选择值**sql**。 为**OperationType**添加另一个筛选器。 然后，关系图显示所选时间段内不同操作的服务器端延迟。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

每个操作的**服务器端延迟**指标显示如下图所示：

![服务器端延迟指标的筛选器](./media/monitor-server-side-latency/server-side-latency-filters.png)

还可以通过使用 "**应用拆分**" 选项对度量值进行分组。  

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure Cosmos DB 数据。
* [审核 Azure Cosmos DB 控制平面操作](audit-control-plane-logs.md)