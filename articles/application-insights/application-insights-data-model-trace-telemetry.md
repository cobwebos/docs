---
title: "Azure Application Insights 遥测数据模型 - 跟踪遥测 | Microsoft Docs"
description: "适用于跟踪遥测的 Application Insights 数据模型"
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
ms.openlocfilehash: e7825b26c47a80debf92be1ad166e5a18bae4a61
ms.lasthandoff: 04/22/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>跟踪遥测：Application Insights 数据模型

跟踪遥测表示搜索文本的 `printf` 样式跟踪语句。 `Log4Net`、`NLog` 和其他基于文本的日志文件条目将转换成此类型的实例。 跟踪没有作为可扩展性的度量。

## <a name="message"></a>消息

跟踪消息。

最大长度：32768 个字符

## <a name="severity-level"></a>严重性级别

跟踪严重性级别。 值可以是 `Verbose`、`Information`、`Warning`、`Error`、`Critical`。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>后续步骤

- 请参阅[数据模型](/application-insights-data-model.md)，了解 Application Insights 的类型和数据模型。
- [在 Application Insights 中浏览 .NET 跟踪日志](/app-insights-asp-net-trace-logs.md)。
- [在 Application Insights 中浏览 Java 跟踪日志](/app-insights-java-trace-logs.md)。
- 查看 Application Insights 支持的[平台](/app-insights-platforms.md)。

