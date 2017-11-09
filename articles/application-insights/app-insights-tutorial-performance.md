---
title: "使用 Azure Application Insights 诊断性能问题| Microsoft Docs"
description: "本教程介绍如何使用 Azure Application Insights 查找和诊断应用程序中的性能问题。"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0edec15c7f14ee5338555b03700b7be32c3a1023
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>使用 Azure Application Insights 查找和诊断性能问题

Azure Application Insights 从应用程序收集遥测，以帮助分析操作和性能。  可以使用此信息确定可能发生的问题，或识别对用户影响最大的应用程序改进。  本教程将从应用程序服务器组件和客户端这两方面介绍分析性能的过程。  学习如何：

> [!div class="checklist"]
> * 识别服务器端操作的性能
> * 分析服务器操作，确定性能减慢的根本原因
> * 识别最慢的客户端操作
> * 使用查询语言分析页面视图的详细信息


## <a name="prerequisites"></a>先决条件

完成本教程：

- 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](app-insights-asp-net.md)。
- 对应用程序[启用 Application Insights profiler](app-insights-profiler.md#installation)。

## <a name="log-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="identify-slow-server-operations"></a>识别缓慢的服务器操作
Application Insights 收集应用程序中不同操作的性能详细信息。  识别持续时间最长的操作，可以诊断潜在问题，或更有针对性地进行开发，以提高应用程序总体性能。

1. 选择“Application Insights”，然后选择订阅。  
1. 选择“调查”下的“性能”菜单或单击“服务器响应时间”图，打开“性能”面板。

    ![性能](media/app-insights-tutorial-performance/performance.png)

2. “性能”面板将显示应用程序每项操作的计数和平均持续时间。  此信息可用于识别对用户影响最大的操作。 在此示例中，很可能应调查“GET Customers/Details”和“GET Home/Index”，因为它们持续时间相对较长、调用次数相对较多。  其他操作可能持续时间更长，但调用次数极少，因此改进它们可能收效甚微。  

    ![“性能”面板](media/app-insights-tutorial-performance/performance-blade.png)

3. 此图正显示一段时间内所有操作的平均持续时间。  将要调查的操作固定到图中以添加该操作。  此图显示，存在值得调查的峰值。  在图上缩短时间窗口，进一步隔离出这些峰值。

    ![“固定”操作](media/app-insights-tutorial-performance/pin-operations.png)

4.  单击要在右侧查看其性能面板的操作。 这将显示不同请求的持续时间分布。  用户通常会注意到半秒左右的响应时间，并将其判定为性能缓慢，因此请缩小 500 毫秒以上的请求时间窗口。  

    ![持续时间分布](media/app-insights-tutorial-performance/duration-distribution.png)

5.  在此示例中，可以看到大量请求超过一秒才得到处理。 单击“操作详细信息”可查看此操作的详细信息。

    ![操作详细信息](media/app-insights-tutorial-performance/operation-details.png)

6.  目前收集到的信息只能确定存在性能缓慢，但很难确定根本原因。  “探查器”可显示操作所运行的实际代码和每步所需的时间，从而帮助确定根本原因。 由于探查器周期性运行，所以可能未记录到某些操作。  随着时间推移，应可记录到更多操作。  若要对操作启用探查器，请单击“探查器跟踪”。
5.  跟踪显示每个操作的单个事件，以便诊断整个操作持续时间的根本原因。  单击持续时间最长的示例。
6.  单击“显示热路径”，以突出显示对操作总持续时间贡献最大的一系列特定事件。  在此示例中，可以看到最慢的调用来自 *FabrikamFiberAzureStorage.GetStorageTableData* 方法。 最耗时的部分是 *CloudTable.CreateIfNotExist* 方法。 如果每次调用该函数时都执行这行代码，则将使用不必要的网络调用和 CPU 资源。 修复该代码的最佳方式是将此行放入仅执行一次的某个启动方法中。 

    ![探查器详细信息](media/app-insights-tutorial-performance/profiler-details.png)

7.  屏幕顶部的“性能提示”对“持续时间过长是由等待造成的”这一评估结果提供了支持。  单击“等待”链接可获得解释不同事件类型的文档。

    ![性能提示](media/app-insights-tutorial-performance/performance-tip.png)

8.  如需进一步分析，可单击“下载 .etl 跟踪”，将跟踪信息下载到 Visual Studio。

## <a name="use-analytics-data-for-server"></a>使用服务器的分析数据
Application Insights Analytics 提供丰富的查询语言，可用于分析 Application Insights 收集的所有数据。  可以用它对请求和性能数据进行深度分析。

1. 返回到“操作详细信息”面板，然后单击“分析”按钮。

    ![“分析”按钮](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics 随即打开，包含对面板中每个视图的查询。  可以直接运行这些查询，或根据需要对其进行修改。  第一个查询显示此操作随时间推移的持续时间。

    ![分析](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>标识缓慢的客户端操作
Application Insights 不仅可以识别要优化的服务器进程，还可以从客户端浏览器的角度进行分析。  这有助于确定应对客户端组件进行的改进，甚至识别与不同浏览器或不同位置有关的问题。

1. 选择“调查”下的“浏览器”，打开浏览器摘要。  这从浏览器的角度提供了应用程序各项遥测的可视化摘要。

    ![浏览器摘要](media/app-insights-tutorial-performance/browser-summary.png)

2.  向下滚动到“最慢的页面”。  这将显示客户端加载用时最长的应用程序页面列表。  可以利用此信息优先处理对用户影响最大的页面。
3.  单击某个页面，打开“页面视图”面板。  在示例中，“/FabrikamProd”页面显示出过长的平均持续时间。  “页面视图”面板提供此页面的详细信息，包括不同持续时间范围的细分。

    ![页面视图](media/app-insights-tutorial-performance/page-view.png)

4.  单击最长的持续时间以调查这些请求的详细信息。  然后单击单个请求以查看客户端请求页面的详细信息，包括浏览器类型及其位置。  此信息有助于确定性能问题是否与特定类型的客户端相关。

    ![请求详细信息](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>使用针对客户端的分析数据
与针对服务器性能收集的数据一样，Application Insights 允许使用 Analytics 深度分析所有客户端数据。

1. 返回到浏览器摘要，单击“分析”图标。

    ![“分析”图标](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics 随即打开，包含对面板中每个视图的查询。 第一个查询显示不同页面视图随时间推移的持续时间。

    ![分析](media/app-insights-tutorial-performance/client-analytics.png)

3.  智能诊断是 Application Insights Analytics 的一项功能，用于标识数据中的唯一模式。  单击折线图中的智能诊断点时，将运行相同查询但排除导致异常的记录。  查询的注释部分显示了这些记录的详细信息，因此可以识别出导致持续时间过长的页面视图的属性。

    ![智能诊断](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>后续步骤
既已了解如何标识运行时异常，可继续学习下一个教程，了解如何创建警报以响应失败。

> [!div class="nextstepaction"]
> [根据应用程序运行状况发出警报](app-insights-tutorial-alert.md)
