---
title: Azure Cosmos DB 中基于消耗量的无服务器产品/服务
description: 详细了解 Azure Cosmos DB 基于消耗量的无服务器产品/服务。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: ef681f861a14fbbf86e7e350441d05f84fc95f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88757891"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB 无服务器（预览版）

> [!IMPORTANT]
> Azure Cosmos DB 无服务器目前处于预览状态。 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 无服务器让你以一种基于消耗的方式使用 Azure Cosmos 帐户，在这种方式下，你只需为数据库操作所消耗的请求单位和数据所消耗的存储空间付费。 在无服务器模式下使用 Azure Cosmos DB 时不涉及最低费用。

> [!IMPORTANT] 
> 你是否有任何关于无服务器的反馈？ 我们想听一听！ 可以随意向 Azure Cosmos DB 无服务器团队发送消息：[azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com)。

使用 Azure Cosmos DB 时，每个数据库操作都具有成本，以[请求单位](request-units.md)进行表示。 此成本的收费方式取决于你使用的 Azure Cosmos 帐户的类型：

- 在[预配吞吐量](set-throughput.md)模式下，你必须承诺在数据库和容器上预配的特定吞吐量（以每秒请求单位数进行表示）。 随后从每秒可用的请求单位数中扣除数据库操作的成本。 在计费周期结束时，会针对你预配的吞吐量进行计费。
- 在无服务器模式下，无需在 Azure Cosmos 帐户中创建容器时预配任何吞吐量。 在计费周期结束时，会针对你的数据库操作已使用的请求单位数进行计费。

## <a name="use-cases"></a>用例

Azure Cosmos DB 无服务器最适合你预计会出现以下情况的方案：

- 低流量：因为在这类情况下预配容量不是必需的，并且可能会导致成本高昂
- 中等突发性：因为无服务器容器每秒最多可提供 5,000 个请求单位
- 中等性能：因为无服务器容器具有[特定性能特征](#performance)

出于这些原因，对于以下类型的工作负荷，应考虑使用 Azure Cosmos DB 无服务器：

- 开发
- 测试
- 原型制作
- 概念证明
- 具有低流量的非关键应用程序

有关如何选择最适合于用例的产品/服务的详细指南，请参阅[如何在预配吞吐量与无服务器之间进行选择](throughput-serverless.md)。

## <a name="using-serverless-resources"></a>使用无服务器资源

无服务器是一种新的 Azure Cosmos 帐户类型，这意味着在创建新帐户时，必须在预配吞吐量与无服务器之间进行选择 。 必须创建新的无服务器帐户，才能开始使用无服务器。 在预览版期间，创建新的无服务器帐户的唯一受支持方法是[使用 Azure 门户](create-cosmosdb-resources-portal.md)。 当前不支持从/向无服务器模式迁移现有帐户。

> [!NOTE]
> 目前只有 Azure Cosmos DB 核心 (SQL) API 支持无服务器。

任何在无服务器帐户中创建的容器都是无服务器容器。 无服务器容器公开的功能与在预配吞吐量模式下创建的容器相同，因此可以通过完全相同的方式读取、写入和查询数据。 不过，无服务器帐户和容器还具有特定特征：

> [!IMPORTANT]
> 当无服务器正式发布时，其中一些限制可能会被删除，你的反馈将有助于我们做出决定！ 请与我们联系并告诉我们有关无服务器体验的详细信息：[azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com)。

- 无服务器帐户只能在单个 Azure 区域中运行。 创建无服务器帐户之后，不能向该帐户添加其他 Azure 区域。
- 不能对无服务器帐户启用 [Synapse Link 预览功能](synapse-link.md)。
- 无服务器容器不需要预配吞吐量，因此以下陈述适用于这种容器：
    - 创建无服务器容器时，不能传递任何吞吐量，否则会返回错误。
    - 不能读取或更新无服务器容器的吞吐量，否则会返回错误。
    - 不能在无服务器帐户中创建共享吞吐量数据库，否则会返回错误。
- 无服务器容器可以提供的最大突发吞吐量为每秒 5,000 个请求单位。
- 无服务器容器最多可存储 50 GB 的数据和索引。

## <a name="monitoring-your-consumption"></a>监视消耗量

如果以前在预配吞吐量模式下使用了 Azure Cosmos DB，则会发现当流量证明预配容量不合理时，无服务器更加经济高效。 权衡之处在于成本会不太容易预测，因为是基于数据库处理的请求数进行计费。 因此，请务必注意当前消耗量。

当浏览帐户的“指标”窗格时，会在“概述”选项卡下找到名为“已消耗的请求单位”的图表  。此图表显示帐户已使用的请求单位数：

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="显示已消耗的请求单位的图表" border="false":::

使用 Azure Monitor 时，可以找到相同的图表，如[此处](monitor-request-unit-usage.md)所述。 请注意，Azure Monitor 允许设置[警报](../azure-monitor/platform/alerts-metric-overview.md)，可用于在请求单位消耗量超过特定阈值时通知你。

## <a name="performance"></a><a id="performance"></a>性能

无服务器资源会产生与预配吞吐量资源提供的性能特征不同的特定性能特征：

- **可用性**：在无服务器产品/服务公开发布之后，如果未使用可用性区域（区域冗余），则无服务器容器可用性的服务级别协议 (SLA) 为 99.9%。 使用可用性区域时，SLA 为 99.99%。
- **延迟**：在无服务器产品/服务公开发布之后，无服务器容器延迟的服务级别目标 (SLO) 对于点读取为 10 毫秒或更少，对于写入为 30 毫秒或更少。 点读取操作包含按其 ID 和分区键值提取单个项。
- 突发性：在无服务器产品/服务公开发布之后，无服务器容器突发性的服务级别目标 (SLO) 为 95%。 这意味着至少在 95% 的时间内可以获得最大突发性。

> [!NOTE]
> 与任何 Azure 预览版一样，Azure Cosmos DB 无服务器不包括在服务级别协议 (SLA) 中。 上面提到的性能特征作为此产品/服务在公开发布时提供的功能的预览版进行提供。

## <a name="next-steps"></a>后续步骤

可通过以下文章开始使用无服务器：

- [Azure Cosmos DB 中的请求单位](request-units.md)
- [在预配吞吐量和无服务器之间进行选择](throughput-serverless.md)
- [Azure Cosmos DB 中的定价模型](how-pricing-works.md)
