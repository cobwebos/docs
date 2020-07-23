---
title: 使用 Application Insights 监视 Azure Functions 上运行的应用程序 - Azure Monitor | Microsoft Docs
description: Azure Monitor 与 Azure Functions 上运行的应用程序无缝集成，可用来监视应用的性能并立即查明应用中的问题。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 093448ad0280ada039f1d4e5abd0e83e4be19b03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482099"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>使用 Azure Monitor Application Insights 监视 Azure Functions

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 内置集成了 Azure Application Insights 来监视函数。 

Application Insights 收集日志、性能和错误数据，并自动检测性能异常。 Application Insights 包含功能强大的分析工具，可帮助你诊断问题并了解函数的使用方式。 当你能够洞察应用程序数据后，你可以持续改善性能和可用性。 你甚至可以在本地函数应用项目开发过程中使用 Application Insights。 

Azure Functions 中内置了必需的 Application Insights 检测。 你只需提供有效的检测密钥便可将函数应用连接到 Application Insights 资源。 在 Azure 中创建函数应用资源时，应将检测密钥添加到应用程序设置中。 如果函数应用还没有此密钥，你可以手动设置此密钥。 有关详细信息，请参阅[监视 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd)。

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>针对 Windows 上的 Java 应用程序的分布式跟踪（公共预览版）

> [!IMPORTANT]
> 此功能目前处于 Windows 上的 Java Azure Functions 的公共预览版中，不支持对 Linux 上的 Java Azure Functions 进行分布式跟踪。 对于消耗计划，它的冷启动时间为8-9 秒。

如果你的应用程序是以 Java 编写的，则你可以通过函数应用程序查看更丰富的数据，包括请求、依赖项、日志和指标。 借助更多数据，你还可以查看并诊断端到端事务，以及查看应用程序映射，该映射聚合了许多事务来显示一个拓扑视图，可以在其中了解各个系统如何交互，以及平均性能和错误率。

端到端诊断和应用程序映射可以洞察每个事务/请求。 将这两个功能结合使用，对于找出每个请求出现可靠性问题和性能瓶颈的根本原因非常有帮助。

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>如何为 Java 函数应用启用分布式跟踪？

导航到函数应用的“概述”边栏选项卡并转到“配置”。 在“应用程序设置”下，单击“+ 新建应用程序设置”。 添加使用以下值的下面两个应用程序设置，然后单击左上方的“保存”。 大功告成！

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>后续步骤

* 在[监视 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) 中了解有关监视功能的更多说明和信息。
* 查看[分布式跟踪](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)的概述
* 了解[应用程序映射](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net)对业务起到的作用
* 了解 [Java 应用的请求和依赖项](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* 详细了解 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 和 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)