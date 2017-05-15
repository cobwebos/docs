---
title: "Azure Application Insights 遥测数据模型 - 指标遥测 | Microsoft Docs"
description: "适用于指标遥测的 Application Insights 数据模型"
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
ms.author: sergkanz
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 1bedede1934141927a770c97b61aeb5218652b3e
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="metric-telemetry-application-insights-data-model"></a>指标遥测：Application Insights 数据模型

[Application Insights](app-insights-overview.md) 支持两种类型的指标遥测：单个度量和预聚合指标。 单个度量只是一个名称和值。 预聚合指标指定在聚合时间间隔内指标的最小和最大值，以及它的标准偏差。

预聚合指标遥测假定聚合期间为一分钟。

Application Insights 支持几个已知的指标名称。 

表示系统计数器和进程计数器的指标：

| **.NET 名称**             | **与平台无关的名称** | **REST API 名称** | **说明**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | 工作正在进行... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | 总计算机 CPU 百分比
| `\Memory\Available Bytes`                 | 工作正在进行... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | 磁盘上的可用内存量
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | 工作正在进行... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | 托管应用程序的进程的 CPU 百分比
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | 工作正在进行... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | 托管应用程序的进程所占用的内存量
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | 工作正在进行... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | 托管应用程序的进程运行的 I/O 操作速率
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | 工作正在进行... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | 应用程序处理的请求速率 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | 工作正在进行... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | 应用程序引发的异常速率
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | 工作正在进行... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | 平均请求执行时间
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | 工作正在进行... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | 队列中等待处理的请求数

## <a name="name"></a>Name

你想要在 Application Insights 门户和 UI 中看到的指标的名称。 

## <a name="value"></a>值

度量的单个值。 要聚合的各个度量值的总和。

## <a name="count"></a>计数

聚合指标的指标权重。 不应为度量设置。

## <a name="min"></a>Min

聚合指标的最小值。 不应为度量设置。

## <a name="max"></a>Max

聚合指标的最大值。 不应为度量设置。

## <a name="standard-deviation"></a>标准偏差

聚合指标的标准偏差。 不应为度量设置。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>后续步骤

- 了解如何使用[用于处理自定义事件和指标的 Application Insights API](app-insights-api-custom-events-metrics.md#send-metrics)。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- 查看 Application Insights 支持的[平台](app-insights-platforms.md)。

