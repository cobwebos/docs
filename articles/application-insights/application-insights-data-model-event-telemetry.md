---
title: "Azure Application Insights 遥测数据模型 - 事件遥测 | Microsoft Docs"
description: "适用于事件遥测的 Application Insights 数据模型"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: 422e193ae10938954602a6ef8c49fd47f473bc01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="event-telemetry-application-insights-data-model"></a>事件遥测：Application Insights 数据模型

可以在 [Application Insights](app-insights-overview.md) 中创建事件遥测项，表示应用程序中发生的事件。 通常它是用户交互，如按钮单击或订单结账。 它还可以是应用程序生命周期事件，如初始化或配置更新。 

事件在语义上不一定与请求关联。 但是，如果使用得当，事件遥测比请求或跟踪更重要。 事件表示业务遥测，应是积极程度较低的单独[采样](app-insights-api-filtering-sampling.md)的主体。

## <a name="name"></a>名称

事件名称。 若要允许适当的分组和有用的指标，请限制应用程序，使其生成少量单独的事件名称。 例如，不要为事件中每个生成的实例使用单独的名称。

最大长度：512 个字符

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>后续步骤

- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- [编写自定义事件遥测](app-insights-api-custom-events-metrics.md#trackevent)
- 查看 Application Insights 支持的[平台](app-insights-platforms.md)。
