---
title: 如何在 Azure Cosmos DB 上的预配吞吐量和无服务器之间进行选择
description: 了解如何在预配的吞吐量和无服务器的工作负荷之间进行选择。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 0adb346a693beaa905438cfdc1249c1646c28811
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608719"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>如何在预配吞吐量和无服务器之间进行选择

Azure Cosmos DB 提供了两种不同的容量模式： [预配吞吐量](set-throughput.md) 和 [无服务器](serverless.md)。 您可以在两种模式下执行完全相同的数据库操作，但对这些操作计费的方式截然不同。 以下视频说明了这些模式之间的核心差异，以及它们如何适应不同类型的工作负荷：

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> 目前仅 Azure Cosmos DB 核心 (SQL) API 支持无服务器。

## <a name="detailed-comparison"></a>详细比较

| 条件 | 预配的吞吐量 | 无服务器 |
| --- | --- | --- |
| 状态 | 正式发布 | 预览 |
| 最适用于 | 需要可预测性能的任务关键型工作负荷 | 具有轻型流量的小型到中型非关键工作负荷 |
| 工作原理 | 对于每个容器，预配一定量的吞吐量，以每秒 [请求单位](request-units.md) 数表示。 每秒，此请求单位数适用于你的数据库操作。 预配的吞吐量可以手动更新或通过自动 [缩放](provision-throughput-autoscale.md)自动进行调整。 | 对容器运行数据库操作，无需预配任何容量。 |
| 每个帐户的限制 | 最大 Azure 区域数：无限制 | 最大 Azure 区域数：1 |
| 每个容器的限制 | 最大吞吐量：无限制<br>最大存储：无限制 | 最大吞吐量： 5000 RU/秒<br>最大存储： 50 GB |
| 性能 | SLA 涵盖99.99% 到99.999% 的可用性<br>针对 SLA 涵盖的点读取和写入 < 10 毫秒延迟<br>SLA 涵盖99.99% 的保证吞吐量 | SLA 涵盖99.9% 到99.99% 的可用性<br>对于 SLO 覆盖的写入，< 10 毫秒的延迟时间，并 < 30 毫秒<br>SLO 涵盖 95% burstability |
| 计费模式 | 对于预配的 RU/s，无论消耗了多少 RU，都按每小时计费。 | 根据数据库操作所使用的 ru 数量，按小时计费。 |

> [!IMPORTANT]
> 当无服务器公开发布时，某些无服务器限制可能减轻或删除， **你的反馈** 将帮助我们做出决定！ 联系并告诉我们有关无服务器体验的详细信息： [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) 。

## <a name="burstability-and-expected-consumption"></a>Burstability 和预期消耗

在某些情况下，是否应为给定的工作负荷选择预配吞吐量或无服务器。 为了帮助进行此决定，可以估计：

- 工作负荷的 **burstability** 要求，即一秒内可能需要使用的最大 ru 数
- 总体 **预期使用量**，即每个月可能使用的 ru 总数 (可以使用 [此处](plan-manage-costs.md#estimating-serverless-costs) 显示的表的帮助来估算此值) 

如果你的工作负荷需要每秒超过 5000 RU，则应选择预配的吞吐量，因为无服务器容器不能超过此限制。 如果不是，则可以根据预期的消耗来比较这两种模式的成本。

**示例 1**：工作负荷应最多突发到 10000 RU/s，并在一个月内总共使用20000000个 RU。

- 仅预配的吞吐量模式可以提供 10000 RU/秒的吞吐量

**示例 2**：工作负荷应最多突发到 500 RU/s，并在一个月内总共使用20000000个 RU。

- 在预配的吞吐量模式下，你可以预配包含 500 RU/s 的容器，每月成本： $0.008 * 5 * 730 = **$29.20**
- 在无服务器模式下，你需要为使用的 ru 付费： $0.25 * 20 = **$5.00**

**示例 3**：工作负荷应最多突发为 500 RU/s，并在一个月内总共使用250000000个 RU。

- 在预配的吞吐量模式下，你可以预配包含 500 RU/s 的容器，每月成本： $0.008 * 5 * 730 = **$29.20**
- 在无服务器模式下，你需要为使用的 ru 支付： $0.25 * 250 = **$62.50**

 (这些示例不考虑存储开销，这在两个模式之间是相同的) 

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 有关最新定价信息，请参阅 [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/) 。

## <a name="next-steps"></a>后续步骤

- 详细了解 [预配吞吐量 Azure Cosmos DB](set-throughput.md)
- 阅读有关[Azure Cosmos DB 无服务器](serverless.md)的详细信息
- 熟悉[请求单位](request-units.md)的概念
