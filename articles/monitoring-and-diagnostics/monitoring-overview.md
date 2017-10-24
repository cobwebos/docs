---
title: "Microsoft Azure 中的监视 | Microsoft Docs"
description: "用于监视 Microsoft Azure 中任何组件的选项。 Azure Monitor、Application Insights 和 Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure 中的监视概述
本文提供对 Microsoft Azure 进行整体监视时所涉及到的工具和服务的概述。 本文适用于：
- 使用 Azure 服务监视 Azure 基础结构和应用程序
- 使用 Azure 服务监视混合与非 Azure 形式的基础结构和应用程序
- 使用非 Azure 服务监视 Azure 基础结构和应用程序

本文讨论了各种可用的服务和产品以及它们如何配合工作。 本文可帮助确定在哪些情况情况最适合使用哪些工具。  

## <a name="why-use-azures-monitoring-services"></a>为何使用 Azure 的监视服务？

云应用中的性能问题可能会影响业务。 使用多个互连的组件和频繁发布版本时，性能随时可能会下降。 开发一款应用后，用户通常会发现其中的问题，而你在测试时却找不到这样的问题。 应该立即知道这些问题，并使用工具来诊断和解决问题。 此外，应用程序中的问题通常是运行这些应用程序的底层基础结构造成的，因此，获得应用程序和基础结构的整体视图是监视 Azure 环境的关键所在。 Microsoft Azure 提供一系列的工具用于识别和解决此类问题。

## <a name="how-do-i-monitor-my-azure-environment"></a>如何监视 Azure 环境？

可通过多种工具来监视 Azure 环境，包括 Azure 上运行的应用程序代码，以及运行该代码的服务和基础结构，等等。 这些工具配合工作，提供全面的云监视，包括：

-   **Azure Monitor** - 以整合式管道形式运行的 Azure 服务，可处理 Azure 服务中的所有监视数据。 在此工具中可以访问性能指标，以及用于描述 Azure 基础结构和所用的任何 Azure 服务的运行情况的事件。 Azure Monitor 是 Azure 环境的监视数据管道，可将该数据直接提供到 Log Analytics 以及第三方工具，可在这些工具中洞察该数据，并将其与本地或其他云资源中的数据合并。

-   **Application Insights** - 提供应用程序性能监视和用户分析的 Azure 服务。 它可以监视编写的代码，以及在 Azure 或本地/其他云中部署的应用程序。 使用 Application Insights SDK 检测应用程序，可以访问一系列数据，包括依赖项响应时间、异常跟踪、调试快照和执行配置文件。 在开发和操作应用程序时，它还提供强大的工具用于分析此应用程序遥测数据。 它与 Visual Studio 深度集成，使你能够立即找到有问题的代码行并解决问题；它还提供使用情况分析，使产品经理能够分析客户对产品的用法。

-   **Log Analytics** - 以前称为 OMS Log Analytics，它是一个 Azure 服务，可从 Azure 服务（通过 Azure Monitor）、Azure VM 和本地或其他云基础结构引入日志与指标数据，并在此数据的基础上提供灵活的日志搜索和现成的分析。 它提供丰富的工具来跨更多的源分析数据，以便针对所有日志运行复杂的查询，并可以根据指定的条件主动发出警报。  甚至可以将自定义数据收集到它的中心存储库，以便查询和可视化这些数据。 还可以利用 Log Analytics 的内置解决方案，立即获取基础结构安全性与功能的见解。

## <a name="accessing-monitoring-in-the-azure-portal"></a>在 Azure 门户中访问监视功能
所有 Azure 监视服务目前在单个 UI 窗格中提供。 有关如何访问此区域的详细信息，请参阅 [Azure Monitor 入门](monitoring-get-started.md)。 

还可以访问特定资源的监视功能，只需突出显示这些资源，并向下钻取到其监视选项即可。 

## <a name="examples-of-when-to-use-which-tool"></a>有关何时使用哪种工具的示例 

以下部分介绍了一些基本方案，以及应该将哪些工具结合使用。 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>方案 1 – 修复开发中的 Azure 应用程序中的错误   

**最好是结合使用 Application Insights、Azure Monitor 和 Visual Studio**

Azure 现在可以在云中提供 Visual Studio 调试器的完整功能。 将 Azure Monitor 配置为向 Application Insights 发送遥测数据 让 Visual Studio 在应用程序中包含 Application Insights SDK。 进入 Application Insights 后，可以使用应用程序映射来直观了解正在运行的应用程序的哪些部件是否正常。 对于不正常的部件，会提供错误和异常用于调查。 可以使用 Application Insights 中的各种分析功能进行更深入的分析。 如果不确定错误的原因所在，可以使用 Visual Studio 调试器来跟踪代码，进一步查明问题。 

有关详细信息，请参阅[监视 Web 应用](../application-insights/app-insights-azure-web-apps.md)，并参考左侧的目录，获取有关各种类型的应用和语言的说明。  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>方案 2 – 调试 Azure.NET Web 应用程序，查明只会出现在生产环境中的错误 

> [!NOTE]
> 这些功能目前以预览版提供。 

**最好是使用 Application Insights，在可能的情况下，还可以使用 Visual Studio 来获得完整的调试体验。**

使用 Application Insights 快照调试器调试应用。 如果生产组件出现某个特定的错误阈值，系统将在称为“快照”的时间窗口内自动捕获遥测数据。 捕获的数据量对于生产云而言是安全的，因为这种数量非常小，还不足以影响性能，但却远远超过了跟踪所需的数量。  系统可以捕获多个快照。 可以在 Azure 门户中查看时间点，或使用 Visual Studio 获得完整体验。 使用 Visual Studio 时，开发人员可以从头到尾浏览快照，就像实时调试一样。 本地变量、参数、内存和帧都可使用。 必须通过 RBAC 角色向开发人员授予对这些生产数据的访问权限。  

有关详细信息，请参阅[快照调试](../application-insights/app-insights-snapshot-debugger.md)。 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>方案 3 – 调试使用容器或微服务的 Azure 应用程序 

**与方案 1 相同。结合使用 Application Insights、Azure Monitor 和 Visual Studio。**Application Insights 还支持从容器中运行的进程以及从微服务（Kubernetes、Docker、Azure Service Fabric）收集遥测数据。 有关详细信息，[请观看这段有关调试容器和微服务的视频](https://go.microsoft.com/fwlink/?linkid=848184)。 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>方案 4 – 解决 Azure 应用程序的性能问题

[Application Insights 探查器](../application-insights/app-insights-profiler.md)可帮助排查此类问题。 可以标识和排查应用服务（Web 应用、逻辑应用、移动应用和 API 应用）及其他计算资源（例如虚拟机、虚拟机规模集 (VMSS)、云服务和 Service Fabric）中运行的应用程序的性能问题。 

> [!NOTE]
> 探查虚拟机、虚拟机规模集 (VMSS)、云服务和 Service Fabric 的功能以预览版提供。   

此外，在出现特定类型的错误（例如页面加载速度缓慢）时，智能检测工具会通过电子邮件主动发出通知。  不需要在此工具中进行任何配置。 有关详细信息，请参阅[智能检测 - 性能异常](../application-insights/app-insights-proactive-performance-diagnostics.md)。



## <a name="next-steps"></a>后续步骤
详细了解以下内容

* [Ignite 2016 的 Azure Monitor 视频](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor 入门](monitoring-get-started.md)
* [Azure 诊断](../azure-diagnostics.md)：如果要尝试诊断云服务、虚拟机、虚拟机规模集或 Service Fabric 应用程序中的问题。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) 如果要尝试诊断应用服务 Web 应用中的问题。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 和 [Operations Management Suite](https://www.microsoft.com/oms/) 生产监视解决方案
