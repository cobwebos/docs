---
title: "Azure Application Insights 遥测数据模型 | Microsoft Docs"
description: "Application Insights 数据模型概述"
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 遥测数据模型

Application Insights 为应用程序性能管理 (APM) 定义遥测数据模型。 此模型可标准化数据收集，并让用户创建与平台和语言无关的监视方案。 Application Insights 收集的数据为典型的应用程序执行模式建模：

![Application Insights 应用程序模型](./media/application-insights-data-model/application-insights-data-model.png)

有两种类型的应用程序：包含可以接收外部***请求***的终结点的应用程序（Web 应用程序），以及会定期“唤醒”以处理某个位置存储的数据的应用程序（Web 作业或函数）。 在这两种情况下，我们将唯一执行称为***操作***。 操作通过***异常***成功或失败，或者可能依赖于其他服务/存储来承载其业务逻辑。 为了反映这些概念，Application Insights 数据模型定义了三种遥测类型：[请求](./application-insights-data-model-request-telemetry.md)、[异常](/application-insights-data-model-exception-telemetry.md)和[依赖项](/application-insights-data-model-dependency-telemetry.md)。

通常，这些类型由应用程序框架定义，并由 SDK 自动收集。 `ASP.NET MVC` 定义某个请求执行在其模型-视图-控制器管道中的表示法 - 标记请求的开始和停止。 对 SQL 的依赖项调用由 `System.Data` 定义。 对 HTTP 终结点的调用由 `System.Net` 定义。 可以使用自定义属性和度量值扩展特定平台和框架收集的遥测类型。 但是，在某些情况下，你需要报告自定义遥测。 你可能需要使用某个熟悉的检测框架（例如 `Log4Net` 或 `System.Diagnostics`）实现诊断日志记录。 或者，你可能需要捕获用户与服务的交互以分析使用模式。 Application Insights 识别其他三种数据类型：[跟踪](/application-insights-data-model-trace-telemetry.md)、[事件](/application-insights-data-model-event-telemetry.md)和[指标](/application-insights-data-model-metric-telemetry.md)，可为这些方案建模。

Application Insights 遥测模型定义了将遥测与它所属的操作进行[关联](/correlation.md)的方式。 例如，请求可以发出 SQL 数据库调用并记录诊断信息。 可为那些要绑定回到请求遥测的遥测项设置关联上下文。

## <a name="schema-improvements"></a>架构改进

Application Insights 数据模型采用简单基本的结构，不过，它能够非常有效地为应用程序遥测建模。 我们将努力保持该模型的精简结构，以便支持基本方案，并让用户根据高级用途扩展架构。

若要报告数据模型或架构问题以及提供建议，请使用 GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) 存储库。

## <a name="next-steps"></a>后续步骤

- 查看 Application Insights 支持的[平台](/app-insights-platforms.md)。
- 了解如何[扩展和筛选遥测](/app-insights-api-filtering-sampling.md)。
- 使用[采样](/app-insights-sampling.md)基于数据模型最小化遥测量。

