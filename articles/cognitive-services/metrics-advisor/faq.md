---
title: 指标顾问常见问题
titleSuffix: Azure Cognitive Services
description: 有关指标 Advisor 服务的常见问题解答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: e4a75bdd6147ee2189660c37062c5bec9d55d512
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631727"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>指标顾问常见问题

### <a name="what-is-the-cost-of-my-instance"></a>实例的成本是多少？

在预览期间，当前不需要使用实例。

### <a name="why-is-the-demo-website-readonly"></a>为什么演示网站为只读？

[演示网站](https://anomaly-detector.azurewebsites.net/)已公开发布。 此实例变为只读，以防止意外上载任何数据。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>为什么无法创建资源？ "定价层" 不可用，显示 "你已为此订阅创建了1个 S0"？

:::image type="content" source="media/pricing.png" alt-text="F0 资源已存在时的消息":::

公共预览期间，只允许在一个区域中的一个订阅下创建指标顾问的一个实例。

如果已在同一区域中使用相同的订阅创建了一个实例，则可以尝试不同的区域或不同的订阅来创建新的实例。 还可以删除现有实例以创建新实例。

如果已删除现有实例，但仍看到此错误，请在删除资源后等待大约20分钟，然后再创建新的实例。

## <a name="basic-concepts"></a>基本概念

### <a name="what-is-multi-dimensional-time-series-data"></a>什么是多维时序数据？

请参阅术语表中的 [多维指标](glossary.md#multi-dimensional-metric)  定义。

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>指标顾问启动异常检测需要多少数据？

至少一个数据点可以触发异常情况检测。 不过，这并不能带来最佳准确性。 该服务将使用您在数据馈送创建期间指定为 "填充间隔" 规则的值来假定以前数据点的窗口。

建议在要检测的时间戳之前包含一些数据。
根据数据的粒度，推荐的数据量会根据下面的情况而变化。

| 粒度 | 用于检测的建议数据量 |
| ----------- | ------------------------------------- |
| 不到5分钟 | 4天的数据 |
| 5分钟到1天 | 28天的数据 |
| 超过1天，到31天 | 4年的数据 |
| 超过31天 | 48年的数据 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>为什么指标顾问不会从历史数据中检测到异常？

指标顾问旨在检测实时流式处理数据。 此服务将查看并运行异常检测的历史数据的最大长度限制。 这意味着，只有在某些最早时间戳之后的数据点才会有异常检测结果。 最早的时间戳取决于数据的粒度。

根据数据的粒度，会有异常检测结果的历史数据的长度如下所示。

| 粒度 | 异常情况检测的历史数据的最大长度 |
| ----------- | ------------------------------------- |
| 不到5分钟 | 板载时间-13 小时 |
| 5分钟到小于1小时 | 板载时间-4 天  |
| 1小时到1天以内 | 板载时间-14 天  |
| 1 天 | 板载时间-28 天  |
| 超过1天，小于31天 | 板载时间-2 年  |
| 超过31天 | 板载时间-24 年   |

### <a name="more-concepts-and-technical-terms"></a>更多概念和技术术语

有关详细信息，另请参阅 [词汇表](glossary.md) 。

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>如何实现编写有效的查询以引入我的数据吗？  

要使指标顾问引入数据，您需要创建一个查询，该查询将以单个时间戳返回数据的尺寸。 指标顾问将多次运行此查询，以从每个时间戳获取数据。 

请注意，查询应在给定时间戳为每个维度组合最多返回一条记录。 返回的所有记录必须具有相同的时间戳。 查询不应返回重复记录。

例如，假设你在下面创建了一个每日度量值的查询： 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

确保为时序使用正确的粒度。 对于每小时指标，你应使用： 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

请注意，这些查询仅以单个时间戳返回数据，并包含由度量顾问引入的所有维度组合。 

:::image type="content" source="media/query-result.png" alt-text="F0 资源已存在时的消息" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>如何实现检测峰值 & 异常？

如果预定义了硬阈值，实际上可以在 [异常检测配置](how-tos/configure-metrics.md#anomaly-detection-methods)中手动设置 "硬阈值"。
如果没有阈值，可以使用 AI 提供支持的 "智能检测"。 有关详细信息，请参阅 [优化检测配置](how-tos/configure-metrics.md#tune-the-detecting-configuration) 。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>如何实现检测具有常规 (季节性的 inconformity 是否为异常) 模式？

"智能检测" 可以了解包含季节性模式的数据的模式。 然后，它将检测不符合常规模式的数据点作为异常。 有关详细信息，请参阅 [优化检测配置](how-tos/configure-metrics.md#tune-the-detecting-configuration) 。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>如何实现将平面线条作为异常检测？

如果数据通常非常不稳定，并且您希望在其变得太稳定或甚至变成平面线时收到警报，则可以将 "更改阈值" 配置为在更改太小时检测此类数据点。
有关详细信息，请参阅 [异常情况检测配置](how-tos/configure-metrics.md#anomaly-detection-methods) 。

## <a name="next-steps"></a>后续步骤
- [指标顾问概述](overview.md)
- [试用演示网站](quickstarts/explore-demo.md)
- [使用 Web 门户](quickstarts/web-portal.md)