---
title: 使用 Azure Application Insights 诊断性能问题| Microsoft Docs
description: 本教程介绍如何使用 Azure Application Insights 查找和诊断应用程序中的性能问题。
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79223674"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>使用 Azure Application Insights 查找和诊断性能问题

Azure Application Insights 从应用程序收集遥测，以帮助分析操作和性能。  可以使用此信息确定可能发生的问题，或识别对用户影响最大的应用程序改进。  本教程将从应用程序服务器组件和客户端这两方面介绍分析性能的过程。  学习如何：

> [!div class="checklist"]
> * 识别服务器端操作的性能
> * 分析服务器操作，确定性能减慢的根本原因
> * 识别最慢的客户端操作
> * 使用查询语言分析页面视图的详细信息


## <a name="prerequisites"></a>必备条件

完成本教程：

- 使用以下工作负荷安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](../../azure-monitor/app/asp-net.md)。
- 对应用程序[启用 Application Insights profiler](../../azure-monitor/app/profiler.md#installation)。

## <a name="log-in-to-azure"></a>登录 Azure
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。

## <a name="identify-slow-server-operations"></a>识别缓慢的服务器操作
Application Insights 收集应用程序中不同操作的性能详细信息。 识别持续时间最长的操作，可以诊断潜在问题，或更有针对性地进行开发，以提高应用程序总体性能。

1. 选择“Application Insights”，然后选择订阅  。  
1. 选择“调查”下的“性能”菜单或单击“服务器响应时间”图，打开“性能”面板     。

    ![性能](media/tutorial-performance/1-overview.png)

2. “性能”面板将显示应用程序每项操作的计数和平均持续时间  。  此信息可用于识别对用户影响最大的操作。 在此示例中，很可能应调查“GET Customers/Details”和“GET Home/Index”，因为它们持续时间相对较长、调用次数相对较多   。  其他操作可能持续时间更长，但调用次数极少，因此改进它们可能收效甚微。  

    ![性能服务器面板](media/tutorial-performance/2-server-operations.png)

3. 此图正显示一段时间内所选操作的平均持续时间。 可以切换到第 95 个百分点值，以便查找性能问题。 将要调查的操作固定到图中以添加该操作。  此图显示，存在值得调查的峰值。  在图上缩短时间窗口，进一步隔离出这些峰值。

    ![“固定”操作](media/tutorial-performance/3-server-operations-95th.png)

4.  右侧的性能面板显示所选操作的不同请求的持续时间的分布情况。  缩小窗口，从大约第 95 个百分点值开始。 “前 3 个依赖项”见解卡大致说明了外部依赖项可能是造成事务速度慢的原因。  单击带示例数的按钮即可查看示例列表。 然后即可选择任意示例来查看事务详细信息。

5.  可以大致看到，调用 Fabrikamaccount Azure 表所花的时间占了事务总持续时间的大部分。 也可看到有一个异常导致其失败。 可以单击列表中的任意项，然后就可以在右侧查看其详细信息。 [详细了解事务诊断体验](../../azure-monitor/app/transaction-diagnostics.md)

    ![操作端到端详细信息](media/tutorial-performance/4-end-to-end.png)
    

6.  “探查器”可显示操作所运行的实际代码和每步所需的时间，因而可以更深入地进行代码级别的诊断  。 由于探查器周期性运行，所以可能未记录到某些操作。  随着时间推移，应可记录到更多操作。  若要对操作启用探查器，请单击“探查器跟踪”  。
5.  跟踪显示每个操作的单个事件，以便诊断整个操作持续时间的根本原因。  单击持续时间最长的示例。
6.  单击“热路径”，以突出显示对操作总持续时间贡献最大的一系列特定事件  。  在此示例中，可以看到最慢的调用来自 *FabrikamFiberAzureStorage.GetStorageTableData* 方法。 最耗时的部分是 *CloudTable.CreateIfNotExist* 方法。 如果每次调用该函数时都执行这行代码，则将使用不必要的网络调用和 CPU 资源。 修复该代码的最佳方式是将此行放入仅执行一次的某个启动方法中。

    ![探查器详细信息](media/tutorial-performance/5-hot-path.png)

7.  屏幕顶部的“性能提示”对“持续时间过长是由等待造成的”这一评估结果提供了支持  。  单击“等待”链接可获得解释不同事件类型的文档  。

    ![性能提示](media/tutorial-performance/6-perf-tip.png)

8.   如需进一步分析，可单击“下载跟踪”以下载跟踪  。 可以使用 [PerfView](https://github.com/Microsoft/perfview#perfview-overview) 查看此数据。

## <a name="use-logs-data-for-server"></a>使用服务器的日志数据
 日志提供丰富的查询语言，可用于分析 Application Insights 收集的所有数据。 可以用它对请求和性能数据进行深度分析。

1. 返回到“操作详细信息”面板，然后单击![“日志”图标](media/tutorial-performance/app-viewinlogs-icon.png)**“在日志中查看(分析)”**

2. 日志随即打开，包含对面板中每个视图的查询。  可以直接运行这些查询，或根据需要对其进行修改。  第一个查询显示此操作随时间推移的持续时间。

    ![日志查询](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>标识缓慢的客户端操作
Application Insights 不仅可以识别要优化的服务器进程，还可以从客户端浏览器的角度进行分析。  这有助于确定应对客户端组件进行的改进，甚至识别与不同浏览器或不同位置有关的问题。

1. 在“调查”下选择“浏览器”，然后单击“浏览器性能”，或者在“调查”下选择“性能”，然后切换到“浏览器”选项卡，只需单击右上角的服务器/浏览器切换按钮以打开浏览器性能摘要即可。       这从浏览器的角度提供了应用程序各项遥测的可视化摘要。

    ![浏览器摘要](media/tutorial-performance/8-browser.png)

2. 选择一个操作名称，然后单击右下角的蓝色示例按钮，选择一个操作。 这样会显示端到端事务详细信息，你可以在右侧查看“页面视图属性”。  这样就可以查看客户端请求页面的详细信息，包括浏览器类型及其位置。 此信息有助于确定性能问题是否与特定类型的客户端相关。

    ![页面视图](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>使用客户端的日志数据
与针对服务器性能收集的数据一样，Application Insights 允许使用日志深度分析所有客户端数据。

1. 返回到浏览器摘要，单击![“日志”图标](media/tutorial-performance/app-viewinlogs-icon.png)  “在日志(Analytics)中查看”

2. 日志随即打开，包含对面板中每个视图的查询。 第一个查询显示不同页面视图随时间推移的持续时间。

    ![日志查询](media/tutorial-performance/10-page-view-logs.png)

3.  智能诊断是日志的一项功能，用于标识数据中的唯一模式。 单击折线图中的智能诊断点时，将运行相同查询但排除导致异常的记录。 查询的注释部分显示了这些记录的详细信息，因此可以识别出导致持续时间过长的页面视图的属性。

    ![带智能诊断功能的日志](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>后续步骤
既已了解如何标识运行时异常，可继续学习下一个教程，了解如何创建警报以响应失败。

> [!div class="nextstepaction"]
> [根据应用程序运行状况发出警报](../../azure-monitor/learn/tutorial-alert.md)
