---
title: 如何在 Azure Cosmos DB 中选择合适的吞吐量产品/服务
description: 了解如何为工作负载选择标准（手动）预配的吞吐量和自动缩放配置的吞吐量。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605210"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>如何在标准（手动）和自动缩放预配的吞吐量之间进行选择 

Azure Cosmos DB 支持两种类型或提供预配的吞吐量：标准（手动）和自动缩放。 这两种吞吐量类型都适用于要求高性能和缩放的任务关键型工作负载，并且在吞吐量、可用性、延迟和一致性方面由相同的 Azure Cosmos DB SLA 支持。

本文介绍如何为工作负载选择标准（手动）和自动缩放预配的吞吐量。 

## <a name="overview-of-provisioned-throughput-types"></a>预配的吞吐量类型概述
在深入探讨标准（手动）和自动缩放之间的区别之前，首先要了解 Azure Cosmos DB 中预配的吞吐量的工作原理。 

使用预配的吞吐量时，你可以设置吞吐量，以工作负载所需的每秒请求单位 (RU/s) 为单位。 服务预配支持吞吐量需求所需的容量。 针对服务的数据库操作（如读取、写入和查询）会消耗一定数量的请求单元 (RUs)。 详细了解[请求单元](request-units.md)。

下表显示了标准（手动）和自动缩放之间的高级比较。

|说明|标准（手动）|自动缩放|
|-------------|------|-------|
|最适用于|具有稳定或可预测流量的工作负载|具有可变或不可预测流量的工作负载。 请参阅[自动缩放的用例](provision-throughput-autoscale.md#use-cases-of-autoscale)。|
|工作原理|除非手动更改 RU/s `T`，否则将预配一组随时间变化的静态 RU/s `T`。 每秒最多可以使用 `T` RU/s 吞吐量。 <br/><br/>例如，如果设置标准（手动）400 RU/s，吞吐量将保持在 400 RU/s。|你设置了不希望系统超过的最高或最大 RU/s `Tmax`。 系统会自动调整吞吐量 `T`，以便 `0.1* Tmax <= T <= Tmax`。 <br/><br/>例如，如果将自动缩放最大 RU/s 设置为 4000 RU/s，系统将在 400 - 4000 RU/s 之间缩放。|
|何时使用|你需要手动管理吞吐量 (RU/s) 并自行缩放。<br/><br/>具有高、一致的预配 RU/s 利用率。 在一个月的所有小时中，如果设置了预配的 RU/s `T`，并且使用了 66% 或更多小时的全额，那么估计你将使用标准（手动）预配的 RU/s 来节省时间。<br/><br/>这是基于在标准（手动）中设置 `T` 与在自动缩放中设置相同数量 `Tmax` 之间的比较。 |你希望 Azure Cosmos DB 根据使用情况管理吞吐量 (RU/s) 和缩放。<br/><br/>使用的是可变或难以预测的 RU/s 使用情况。 在一个月的所有小时中，如果设置了自动缩放最大 RU/s `Tmax`，并使用了 66% 或更少小时的全额 `Tmax`，那么估计你将使用自动缩放来节省时间。<br/><br/>这是基于在标准（手动）吞吐量中设置自动缩放 `Tmax` 和相同数量的 `T` 之间的比较。|
|计费模式|对于预配的 RU/s，无论消耗了多少 RU，都按每小时计费。<br/><br/>示例： <li>预配 400 RU/s</li><li>小时 1：无请求</li><li>小时 2：400 RU/s 的请求数</li><br/><br/>对于小时 1 和小时 2，将以[标准（手动）速率](https://azure.microsoft.com/pricing/details/cosmos-db/)为这两个小时收取 400 RU/s 的费用。|计费以小时为单位，将按系统在一小时内缩放到的最高 RU/s。 <br/><br/>示例： <li>预配自动缩放最大 RU/s 为 4000 RU/s（缩放范围在 400 - 4000 RU/s 之间）</li><li>小时 1：系统扩展到最高值 3500 RU/s</li><li>小时 2：由于没有使用，系统缩减到最小 400 RU/s（总是 `Tmax` 的 10%）</li><br/><br/>将在第 1 小时收到 3500 RU/s 的计费，在第 2 小时收到 400 RU/s 的计费，按[自动缩放预配的吞吐量率](https://azure.microsoft.com/pricing/details/cosmos-db/)。 每 RU/s 的自动缩放速率为 1.5 * 标准（手动）速率。
|如果超过了预配的 RU/s，会发生什么情况|RU/s 在预配的位置保持静态。 任何在一秒钟内消耗超过预配的 RU 的请求都将受到速率限制，响应建议在重试之前等待一段时间。 如果需要，可以手动增加或降低 RU/s。| 系统将把 RU/s 扩展到自动缩放最大 RU/s。 任何在一秒钟内消耗超过自动缩放最大 RU 的请求都将受到速率限制，响应建议在重试之前等待一段时间。|

## <a name="understand-your-traffic-patterns"></a>了解流量模式

### <a name="new-applications"></a>新应用程序 ###

如果你正在生成一个新应用程序，但还不知道流量模式，你可能想要从入口点 RU/s（或最小 RU/s）开始，以避免在开始时过度预配。 或者，如果你有一个不需要大规模的小型应用程序，你可能只需要预配最小的入口点 RU/s 来优化成本。 对于预期流量较低的小型应用程序，还可以考虑[无服务器](throughput-serverless.md)容量模式。

无论计划使用标准（手动）还是自动缩放，都应该考虑以下事项：

如果在 400 RU/s 的入口点预配标准（手动）RU/s，则无法消耗超过 400 RU/s 的吞吐量，除非手动更改吞吐量。 你将以每小时标准（手动）预配的吞吐量为 400 RU/s 计费。

如果在最大 RU/s 为 4000 RU/s 的入口点预配自动缩放吞吐量，则资源将在 400 到 4000 RU/s 之间缩放。 由于每 RU/s 的自动缩放吞吐量计费速率是标准（手动）速率的 1.5 倍，对于系统已缩减到最低 400 RU/s 的小时，你的计费将高于手动预配 400 RU/s 的计费。 但是，使用自动缩放，在任何时候，如果你的应用程序流量激增，则可以在不需要用户操作的情况下消耗高达 4000 RU/s。 通常情况下，你应该权衡一个好处，即能够在任何时候以 1.5 倍的自动缩放速率消耗最大 RU/s。

使用 Azure Cosmos DB [容量计算器](estimate-ru-with-capacity-planner.md)估计吞吐量需求。 

### <a name="existing-applications"></a>现有应用程序 ###

如果现有应用程序使用标准（手动）预配的吞吐量，则可以使用 [Azure Monitor 指标](../azure-monitor/insights/cosmosdb-insights-overview.md)来确定流量模式是否适合自动缩放。 

首先，查找数据库或容器的[规范化请求单位消耗指标](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric)。 规范化利用率是衡量当前使用标准（手动）预配的吞吐量的一个指标。 数值越接近 100%，就越能充分地使用预配 RU/s。 [详细了解](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric)指标。

接下来，确定规范化利用率随时间变化的方式。 如果发现规范化利用率是可变的或不可预测的，请考虑在数据库或容器上启用自动缩放。 相反，如果它是稳定和可预测的，则考虑保持标准（手动）预配的吞吐量。 

> [!TIP]
> 使用标准（手动）吞吐量，可以使用规范化利用率指标来估计切换到自动缩放时可能使用的实际 RU/s。 将某个时间点的规范化利用率乘以当前提供的标准（手动）RU/s。 例如，如果预配了 5000 RU/s，且规范化利用率为 90%，则 RU/s 使用量为 0.9 * 5000 = 4500 RU/s。 如果看到流量模式是可变的，但预配过高或过低，则可能需要启用自动缩放，然后相应地更改自动缩放最大 RU/s 设置。

## <a name="measure-and-monitor-your-usage"></a>测量和监视使用情况
随着时间的推移，在选择吞吐量类型之后，应该监视应用程序并根据需要进行调整。 

使用自动缩放时，请使用 Azure Monitor 查看预配的自动缩放最大 RU/s（自动缩放最大吞吐量）和系统当前缩放到的 RU/s（预配的吞吐量） 。 下面是使用自动缩放的可变或不可预测工作负载的示例。 注意，当没有任何流量时，系统将 RU/s 缩放到最大 RU/s 的 10% 的最小值，在本例中分别为 5000 RU/s 和 50,000 RU/s。 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="使用自动缩放的工作负载示例":::

> [!NOTE]
> 当使用标准（手动）预配的吞吐量时，预配的吞吐量指标指的是作为用户设置的内容。 使用自动缩放吞吐量时，此指标是指系统当前缩放到的 RU/s。

## <a name="next-steps"></a>后续步骤
* 使用 [RU 计算器](https://cosmos.azure.com/capacitycalculator/)估计新工作负载的吞吐量。
* 使用 [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) 监视现有工作负载。
* 了解[如何对 Azure Cosmos 数据库或容器预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)。
* 查看[自动缩放常见问题解答](autoscale-faq.md)。