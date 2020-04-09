---
title: 如何监视 Azure Cosmos DB 中操作的服务器端延迟
description: 了解如何监视 Azure Cosmos DB 帐户或容器中操作的操作的服务器延迟。 Azure Cosmos DB 帐户的所有者可以了解 Azure Cosmos 帐户的服务器端延迟问题。
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 38f5778e363a16117bab482a758666ec81352afd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887562"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>如何监视 Azure Cosmos DB 容器或帐户中操作的服务器端延迟

Azure Cosmos DB 的 Azure 监视器提供了一个指标视图，用于监视您的帐户并创建仪表板。 默认情况下，Azure Cosmos DB 指标是收集的，此功能不需要您显式启用或配置任何内容。 服务器端延迟指标用于查看操作的服务器端延迟。 Azure Cosmos DB 为具有直接连接的点读取/写入操作提供小于 10 毫秒的 SLA。 对于点读取和写入操作，SLA 的计算方式如下[SLA 文档中](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)的详细信息。

如果看到点操作的异常大延迟，例如：

* 直接模式下具有分区键和 ID 的获取或设置操作
* 读写操作或
* 查询

您可以查找诊断日志以查看返回的数据大小。 如果看到查询操作的持续高延迟，则可以查找诊断日志，了解返回的数据大小、使用的[吞吐量或 RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries)大小，或给定时间段内此类操作的数量。 这样，您可以调试服务器端延迟问题。

## <a name="view-server-side-latency-metric"></a>查看服务器端延迟指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从左侧导航栏中选择 **"监视器"，** 然后选择 **"指标**"。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。**************** 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。************
   
   ![选择 Cosmos DB 帐户以查看指标](./media/monitor-server-side-latency/select-cosmosdb-account.png)

1. 接下来，从可用指标列表中选择**服务器端延迟**指标。 要详细了解此列表中的所有可用指标，请参阅[按类别的指标](monitor-cosmos-db-reference.md)。 在此示例中，让我们选择**服务器端延迟**和**Avg**作为聚合值。 除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。******** 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户选择服务器端延迟指标](./media/monitor-server-side-latency/serverside-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>服务器端延迟的筛选器

您还可以筛选指标和图表显示由特定的**集合名称**、**连接模式**、**数据库名称**、**操作类型**、**区域**和**公共API类型**显示。 

要筛选指标，请选择 **"添加筛选器"** 并选择所需的属性（如**PublicAPIType）** 并选择值**sql**。 为**操作类型**添加另一个筛选器。 然后，该图显示所选时间段内不同操作的服务器端延迟。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

每个操作的**服务器端延迟**指标如下所示：

![服务器端延迟指标的筛选器](./media/monitor-server-side-latency/serverside-latency-filters.png)

您还可以使用 **"应用拆分**"选项对指标进行分组。  

## <a name="next-steps"></a>后续步骤

* 使用 Azure 中的[诊断设置](cosmosdb-monitor-resource-logs.md)监视 Azure 宇宙数据库数据
* [审核 Azure 宇宙 DB 控制平面操作](audit-control-plane-logs.md)