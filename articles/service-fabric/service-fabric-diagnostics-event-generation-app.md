---
title: Azure Service Fabric 应用程序级别监视 | Microsoft Docs
description: 了解用于监视和诊断 Azure Service Fabric 群集的应用程序和服务级别事件和日志。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: b8118d83e2be452c6aa5bbc8b7a3c220d26903a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="application-and-service-level-logging"></a>应用程序和服务级别日志记录

对于服务监视的其他大多数环节，检测代码是一个基本要求。 我们只能通过检测来判断是否出错以及诊断需要解决哪些问题。 尽管在技术上可将调试器连接到生产服务，但这种做法并不常见。 因此，提供详细的检测数据非常重要。

某些产品可自动检测代码。 尽管这些解决方案能够正常运行，但几乎始终都要执行手动检测。 最后，必须提供足够的信息来对应用程序进行取证式的调试。 本文将介绍检测代码的不同方法，以及如何在不同的方法之间做出选择。

有关如何使用这些建议的示例，请参阅[向 Service Fabric 应用程序添加日志记录](service-fabric-how-to-diagnostics-log.md)。

## <a name="eventsource"></a>EventSource

在 Visual Studio 中通过模板创建 Service Fabric 解决方案时，将生成 **EventSource** 派生类（**ServiceEventSource** 或 **ActorEventSource**）。 会创建一个模板，可将应用程序或服务的事件添加到其中。 **EventSource** 名称**必须**唯一，应该将它重命名，不要使用默认的模板字符串 MyCompany-&lt;solution&gt;-&lt;project&gt;。 使用多个同名的 **EventSource** 定义会导致运行时出现问题。 每个定义的事件必须具有唯一标识符。 如果标识符不唯一，将发生运行时失败。 某些组织为标识符预先分配了值范围，避免不同的开发团队之间发生冲突。 有关详细信息，请参阅 [Vance 的博客](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文档](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

## <a name="aspnet-core-logging"></a>ASP.NET Core 日志记录

必须认真规划如何检测代码。 适当的检测计划有助于避免代码基变得不稳定，从而需要重新检测代码。 为了降低风险，可以选择一个检测库，例如，Microsoft ASP.NET Core 中包含的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供一个可在所选提供程序中使用的 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 接口，同时尽量减轻对现有代码的影响。 可以在 Windows 和 Linux 上的 ASP.NET Core 中以及整个 .NET Framework 中使用代码，从而将检测代码标准化。

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights 具有现成的与 Service Fabric 的丰富集成。 用户可以添加 AI Service Fabric nuget 包并接收可以在 Azure 门户中查看的已创建和收集的数据和日志。 另外，建议用户添加其自己的遥测数据，以便诊断和调试其应用程序并跟踪哪些服务及其应用程序的哪些部分使用得最多。 该 SDK 中的 [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) 类提供了许多方式来用于在应用程序中跟踪遥测数据。 请在我们提供的有关[监视和诊断 .NET 应用程序](service-fabric-tutorial-monitoring-aspnet.md)的教程中查看有关如何进行检测以及向应用程序添加 Application Insights 的示例。


## <a name="next-steps"></a>后续步骤

选择了用于检测应用程序和服务的日志记录提供程序之后，需要将日志和事件聚合才能将其发送到任何的分析平台。 阅读有关 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)、[EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [WAD](service-fabric-diagnostics-event-aggregation-wad.md) 的信息，更好地了解推荐的一些选项。
