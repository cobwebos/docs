---
title: "Azure Application Insights 遥测数据模型 - 请求遥测 | Microsoft Docs"
description: "适用于请求遥测的 Application Insights 数据模型"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: sergkanz
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5036ce23e602c7723f5fafef60ab45d533c1fe7d
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="request-telemetry-application-insights-data-model"></a>请求遥测：Application Insights 数据模型

[Application Insights](app-insights-overview.md) 中的请求遥测项表示由应用程序的外部请求触发的执行的逻辑序列。 每个请求执行由包含所有执行参数的唯一 `ID` 和 `url` 标识。 可以按逻辑 `name` 将请求分组，并定义此请求的 `source`。 代码执行的结果可能是 `success` 或 `fail`，并且具有特定的 `duration`。 可以进一步根据 `resultCode` 将成功和失败的执行分组。 请求遥测的开始时间在信封级别定义。

请求遥测使用自定义的 `properties` 和 `measurements` 支持标准可扩展性模型。

## <a name="name"></a>Name

请求的名称表示用于处理该请求的代码路径。 较小的基数值对请求分组更有利。 对于 HTTP 请求，该名称表示 HTTP 方法和 URL 路径模板，例如，不带实际 `id` 值的 `GET /values/{id}`。

在字母大小写方面，Application Insights Web SDK 会“按原样”发送请求名称。 UI 上的分组区分大小写，因此 `GET /Home/Index` 被视为不同于 `GET /home/INDEX`，即使两者通常生成相同的控制器和操作执行。 原因在于，URL 往往[区分大小写](http://www.w3.org/TR/WD-html40-970708/htmlweb.html)。 你可能想要确定所有 `404` 是否发生在以大写形式键入的 URL 上。 可在该[博客文章](http://apmtips.com/blog/2015/02/23/request-name-and-url/)中详细了解 ASP.Net Web SDK 如何处理请求名称集合。

最大长度：1024 个字符

## <a name="id"></a>ID

请求调用实例的标识符。 用于在请求与其他遥测项之间建立关联。 ID 应该全局唯一。 有关详细信息，请参阅[关联](application-insights-correlation.md)页。

最大长度：128 个字符

## <a name="url"></a>URL

包含所有查询字符串参数的请求 URL。

最大长度：2048 个字符

## <a name="source"></a>源

请求的源。 示例包括调用方的检测密钥或调用方的 IP 地址。 有关详细信息，请参阅[关联](application-insights-correlation.md)页。

最大长度：1024 个字符

## <a name="duration"></a>持续时间

采用 `DD.HH:MM:SS.MMMMMM` 格式的请求持续时间。 必须是正数且小于 `1000` 天。 此字段是必填的，因为请求遥测表示具有开始和结束时间的操作。

## <a name="response-code"></a>响应代码

请求执行的结果。 HTTP 请求的 HTTP 状态代码。 它可能是 `HRESULT` 值，或者其他请求类型的异常类型。

最大长度：1024 个字符

## <a name="success"></a>成功

指示调用成功还是失败。 此字段是必填的。 如果未显式设置为 `false` - 将请求视为成功。 如果操作被异常中断或者返回错误结果代码，请将此值设置为 `false`。

对于 Web 应用程序，如果响应代码小于 `400` 或等于 `401`，Application Insights 会将请求定义为失败。 但是，在某些情况下，此默认映射与应用程序的语义不匹配。 响应代码 `404` 可能指示“无记录”，这可能是常规流的一部分。 它也可能指示链接断开。 对于断开的链接，甚至可以实现更高级的逻辑。 仅当通过分析 URL 引用站点确定这些断开的链接位于同一站点时，才可以将这些链接标记为失败。 或者，在从公司的移动应用程序访问这些链接时，可将其标记为失败。 同样，在从不支持重定向的客户端访问这些链接时，`301` 和 `302` 将指示失败。

部分接受的内容 `206` 可能指示整个请求失败。 例如，Application Insights 终结点以单个请求的形式接收一批遥测项。 未成功处理该批中的某些项时，该终结点将返回 `206`。 `206` 比率提高指示出现了需要调查的问题。 类似的逻辑适用于 `207` 多状态，其中的成功结果可能是不同响应代码的最差结果。

可在该[博客文章](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/)中详细了解请求结果代码和状态代码。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>后续步骤

- [编写自定义请求遥测](app-insights-api-custom-events-metrics.md#trackrequest)
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- 了解如何使用 Application Insights [配置 ASP.NET Core](app-insights-asp-net.md) 应用程序。
- 查看 Application Insights 支持的[平台](app-insights-platforms.md)。

