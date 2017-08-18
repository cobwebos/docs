---
title: "Azure Application Insights 遥测数据模型 | Microsoft Docs"
description: "Application Insights 数据模型概述"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 587d73bc91aa10b79c1d1488f98f05b73801d8c8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017

---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 遥测数据模型

[Azure Application Insights](app-insights-overview.md) 将遥测从 Web 应用程序发送到 Azure 门户，以便分析应用程序的性能和使用情况。 标准化遥测模型可以创建不依赖于平台和语言的监测。 

Application Insights 收集的数据为典型的应用程序执行模式建模：

![Application Insights 应用程序模型](./media/application-insights-data-model/application-insights-data-model.png)

使用以下类型的遥测监视应用的执行情况。 Application Insights SDK 通常会从 Web 应用程序框架自动收集以下三种类型：

* [请求](application-insights-data-model-request-telemetry.md) - 生成该类型的遥测用以记录应用接收的请求。 例如，Application Insights Web SDK 会自动为 Web 应用接收到的每个 HTTP 请求生成请求遥测项。 

    “操作”是处理请求的执行线程。 还可以[编写代码](app-insights-api-custom-events-metrics.md#trackrequest)监视其他类型的操作，例如 Web 作业或函数中定期处理数据的“唤醒”操作。  每个操作都有一个 ID。 此 ID 可用于将应用处理请求时生成的所有遥测 [分组] \(application-insights-correlation.md)。 每个操作无论成功或失败都需要持续一段时间。
* [异常](application-insights-data-model-exception-telemetry.md) - 通常表示导致操作失败的异常。
* [依赖项](application-insights-data-model-dependency-telemetry.md) - 表示从应用到外部服务或存储（如 REST API 或 SQL）的调用。 在 ASP.NET 中，对 SQL 的依赖项调用由 `System.Data` 定义。 对 HTTP 终结点的调用由 `System.Net` 定义。 

Application Insights 为自定义遥测提供了三种额外的数据类型：

* [跟踪](application-insights-data-model-trace-telemetry.md) - 直接使用或通过适配器使用熟悉的检测框架（如 `Log4Net` 或 `System.Diagnostics`）实现诊断日志记录。
* [事件](application-insights-data-model-event-telemetry.md) - 通常用于捕获用户与服务的交互，以分析使用模式。
* [指标](application-insights-data-model-metric-telemetry.md) - 用于报告定期标量度量。

每个遥测项目均可定义[上下文信息](application-insights-data-model-context.md)，如应用程序版本或用户会话 ID。 上下文是一组强类型化字段，可解锁某些方案。 正确初始化应用程序版本后，Application Insights 可检测应用程序行为中与重新部署相关的新模式。 会话 ID 可用于计算出现中断或问题对用户造成的影响。 错误跟踪或关键异常可计算某些失败依赖项的不同会话 ID 值计数，较好地反映影响。

Application Insights 遥测模型定义了将遥测与它所属的操作进行[关联](application-insights-correlation.md)的方式。 例如，请求可以发出 SQL 数据库调用并记录诊断信息。 可为那些要绑回请求遥测的遥测项设置关联上下文。

## <a name="schema-improvements"></a>架构改进

Application Insights 数据模型采用简单基本的结构，不过，它能够非常有效地为应用程序遥测建模。 我们将努力保持该模型的精简结构，以便支持基本方案，并让用户根据高级用途扩展架构。

若要报告数据模型或架构问题以及提供建议，请使用 GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) 存储库。

## <a name="next-steps"></a>后续步骤

- [编写自定义遥测](app-insights-api-custom-events-metrics.md)
- 了解如何[扩展和筛选遥测](app-insights-api-filtering-sampling.md)。
- 使用[采样](app-insights-sampling.md)基于数据模型最小化遥测量。
- 查看 Application Insights 支持的[平台](app-insights-platforms.md)。

