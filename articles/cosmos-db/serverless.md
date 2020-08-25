---
title: Azure Cosmos DB 中基于消耗的无服务器产品
description: 详细了解 Azure Cosmos DB 基于消耗的无服务器服务。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: ef681f861a14fbbf86e7e350441d05f84fc95f58
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757891"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB 无服务器 (预览版) 

> [!IMPORTANT]
> Azure Cosmos DB 无服务器目前处于预览阶段。 此预览版本在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 无服务器，你可以使用基于消费的方式来使用 Azure Cosmos 帐户，只需为数据库操作使用的请求单位和数据所用的存储付费。 在无服务器模式下使用 Azure Cosmos DB 时，不会产生最小的费用。

> [!IMPORTANT] 
> 你是否有任何有关无服务器的反馈？ 我们想要倾听！ 随意将消息放到 Azure Cosmos DB 无服务器团队： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

使用 Azure Cosmos DB 时，每个数据库操作都以 [请求单位](request-units.md)表示。 按此费用收费的方式取决于所使用的 Azure Cosmos 帐户类型：

- 在 " [预配的吞吐量](set-throughput.md) " 模式下，你必须承诺特定数量的吞吐量 (用在数据库和容器上预配的每秒请求单位数) 表示。 然后，从每秒可用的请求单位数中减去数据库操作的成本。 在计费周期结束时，将根据预配的吞吐量来计费。
- 在无服务器模式下，在 Azure Cosmos 帐户中创建容器时，无需设置任何吞吐量。 在计费周期结束时，你将按数据库操作使用的请求单位数计费。

## <a name="use-cases"></a>用例

Azure Cosmos DB 无服务器最适合的方案：

- **轻型流量**：由于此类情况下的预配容量不是必需的，并且可能会导致成本高昂
- **适中的 burstability**：因为无服务器容器每秒最多可以传递5000个请求单位
- **适中性能**：由于无服务器容器具有 [特定的性能特征](#performance)

出于这些原因，应考虑对以下类型的工作负荷进行 Azure Cosmos DB 无服务器操作：

- 开发
- 测试
- 原型制作
- 概念证明
- 具有轻型流量的非关键应用程序

有关如何选择最适合用例的产品/服务的详细指南，请参阅 [如何在预配吞吐量和无服务器之间进行选择](throughput-serverless.md) 。

## <a name="using-serverless-resources"></a>使用无服务器资源

无服务器是新的 Azure Cosmos 帐户类型，这意味着在创建新帐户时必须在 **预配吞吐量** 和 **无服务器** 之间进行选择。 必须创建新的无服务器帐户，才能开始使用无服务器。 在预览版本中，唯一支持的创建新无服务器帐户的方法是 [使用 Azure 门户](create-cosmosdb-resources-portal.md)。 当前不支持从无服务器模式迁移现有帐户。

> [!NOTE]
> 目前仅 Azure Cosmos DB 核心 (SQL) API 支持无服务器。

任何在无服务器帐户中创建的容器都是无服务器容器。 无服务器容器公开的功能与在预配吞吐量模式下创建的容器相同，因此，你可以以完全相同的方式读取、写入和查询数据。 不过，无服务器帐户和容器也具有特定特征：

> [!IMPORTANT]
> 当无服务器公开发布时，这些限制可能会减轻或删除， **你的反馈** 将帮助我们做出决定！ 联系并告诉我们有关无服务器体验的详细信息： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

- 无服务器帐户只能在单个 Azure 区域中运行。 创建无服务器帐户后，不能向无服务器帐户添加其他 Azure 区域。
- 不能在无服务器帐户上启用 [Synapse 链接预览功能](synapse-link.md) 。
- 无服务器容器上不需要预配吞吐量，因此以下语句适用：
    - 创建无服务器容器时，不能通过任何吞吐量，否则会返回错误。
    - 无法读取或更新无服务器容器上的吞吐量，因此会返回错误。
    - 不能在无服务器帐户中创建共享吞吐量数据库，这样做会返回错误。
- 无服务器容器可提供每秒5000请求单位的最大吞吐量 burstability。
- 无服务器容器最多可存储 50 GB 的数据和索引。

## <a name="monitoring-your-consumption"></a>监视你的消耗

如果以前在预配的吞吐量模式下使用 Azure Cosmos DB，则会发现当流量不会调整预配的容量时，无服务器更具成本效益。 权衡是因为您的成本将会降低，因为您是根据数据库处理的请求数进行计费。 因此，请务必注意当前的使用情况。

浏览帐户的 "**指标**" 窗格时，会在 "**概览**" 选项卡下找到名为 "**请求单位**" 的图表。此图显示了帐户使用了多少个请求单位：

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="显示已使用请求单位的图表" border="false":::

使用 Azure Monitor 时，可以找到相同的图表，如 [此处](monitor-request-unit-usage.md)所述。 请注意，Azure Monitor 允许您设置警报，这些 [警报](../azure-monitor/platform/alerts-metric-overview.md)可用于在请求单位消耗量超过特定阈值时通知您。

## <a name="performance"></a><a id="performance"></a>性能

无服务器资源会产生不同于预配的吞吐量资源提供的特定性能特征：

- **可用性**：在无服务器产品/服务公开发布后，无服务器容器的可用性将被服务级别协议 (SLA) 99.9%，可用性区域 (不会使用区域冗余。 使用可用性区域时，SLA 为99.99%。
- **延迟**： "无服务器" 产品/服务已正式发布后，无服务器容器的延迟将由服务级别目标 (SLO) 为10毫秒或更短（对于点读取）和30毫秒或更短（对于写入）。 点读取操作包含按其 ID 和分区键值提取单个项。
- **Burstability**：在 "无服务器" 产品/服务已正式发布后，服务级别目标 (SLO) 为95%。 这意味着至少95% 的时间内可以获得最大 burstability。

> [!NOTE]
> 任何 Azure 预览版都不会从服务级别协议 (SLA) 中排除 Azure Cosmos DB 无服务器。 上面提到的性能特征作为此产品/服务在公开发布时提供的内容的预览版提供。

## <a name="next-steps"></a>后续步骤

以下文章介绍无服务器入门：

- [Azure Cosmos DB 中的请求单位](request-units.md)
- [在预配吞吐量和无服务器之间进行选择](throughput-serverless.md)
- [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
