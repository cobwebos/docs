---
title: Azure Application Insights - Azure Functions 支持功能 | Microsoft Docs
description: 适用于 Azure Functions 的 Application Insights 支持功能
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496055"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>适用于 Azure Functions 的 Application Insights 支持功能

Azure Functions 提供与 Application Insights 的[内置集成](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)功能（通过 ILogger Interface 可用）。 以下是当前受支持的功能列表。 有关[入门](https://github.com/Azure/Azure-Functions/wiki/App-Insights)信息，请参阅 Azure Functions 指南。

## <a name="supported-features"></a>受支持的功能

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | 2.5.0       | **2.9.1**         |
| | | | 
| **自动集合**        |                 |                   |               
| &bull; 请求                     | 是             | 是               | 
| &bull; 异常                   | 是             | 是               | 
| &bull; 性能计数器         | 是             | 是               |
| &bull; 依赖项                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 是               | 
| | | | 
| **受支持的功能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 是             | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; 安全控制通道|                 | 是               | 
| &bull; 采样                     | 是             | 是               | 
| &bull; 检测信号                   |                 | 是               | 
| | | | 
| **关联性**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 是               | 
| &bull; EventHub                       |                   | 是               | 
| | | | 
| **可配置性**                      |                   |                   |           
| &bull; 完全可配置。<br/>请参阅 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) 了解相应说明。<br/>请参阅 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 了解所有选项。               |                   | 是                   | 


## <a name="performance-counters"></a>性能计数器

性能计数器的自动收集仅适用于 Windows 计算器。


## <a name="live-metrics--secure-control-channel"></a>实时指标和安全控制通道

指定的自定义筛选器条件将发回到 Application Insights SDK 中的“实时指标”组件。 筛选器可能包含 customerID 等敏感信息。 可以使用机密 API 密钥确保通道安全。 有关说明，请参阅[确保控制通道安全](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)。

## <a name="sampling"></a>采样

Azure Functions 默认在其配置中启用采样功能。 有关详细信息，请参阅[配置采样](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)。

如果你的项目依赖于 Application Insights SDK 以执行手动跟踪的遥测数据，可能会遇到奇怪的行为，则不同于 Functions 的采样配置采样配置时。 

我们建议为函数使用相同的配置。 与**Functions v2**，可以获取在构造函数中使用依赖关系注入的相同配置：

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
