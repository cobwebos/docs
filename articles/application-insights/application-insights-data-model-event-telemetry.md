---
title: "Azure Application Insights 遥测数据模型 - 事件遥测 | Microsoft Docs"
description: "适用于事件遥测的 Application Insights 数据模型"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>事件遥测：Application Insights 数据模型

事件表示应用程序中发生操作的时间点。 通常它是用户交互，如按钮单击或订单结账。 它还可以是应用程序生命周期事件，如初始化或配置更新。 事件名称应为较短的低基数字符串。 

事件在语义上不一定与请求关联。 但是，如果使用得当，事件遥测比请求或跟踪更重要。 事件表示业务遥测，应是积极程度较低的单独采样的主体。

## <a name="name"></a>Name

事件名称。 使其保持低基数，以便正确分组和提供有用指标。

最大长度：512 个字符

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>后续步骤

- 请参阅[数据模型](/application-insights-data-model.md)，了解 Application Insights 的类型和数据模型。
- 了解如何使用[用于处理自定义事件和指标的 Application Insights API](/app-insights-asp-net-dependencies.md)。
- 查看 Application Insights 支持的[平台](/app-insights-platforms.md)。

