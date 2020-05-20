---
title: 使用 Azure Application Insights 诊断运行时异常 | Microsoft Docs
description: 本教程介绍如何使用 Azure Application Insights 查找并诊断应用程序中的运行时异常。
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 91a0e4b052571a509ec7122e4440a8eaf58839be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670417"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>使用 Azure Application Insights 查找并诊断运行时异常

Azure Application Insights 从应用程序收集遥测数据，帮助确定和诊断运行时异常。  本教程引导完成对应用程序执行此流程的步骤。  学习如何：

> [!div class="checklist"]
> * 修改项目来实现异常跟踪
> * 确定应用程序不同组件出现的异常
> * 查看异常的详细信息
> * 下载异常的快照以供 Visual Studio 调试
> * 使用查询语言分析失败请求的详细信息
> * 新建工作项来更正故障代码


## <a name="prerequisites"></a>必备条件

完成本教程：

- 使用以下工作负荷安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
- 下载并安装 [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger)。
- 启用 [Visual Studio 快照调试器](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)
- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](../../azure-monitor/app/asp-net.md)。 
- 本教程跟踪应用程序中异常的标识，以便在开发或测试环境中修改代码来生成异常。 

## <a name="log-in-to-azure"></a>登录 Azure
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。


## <a name="analyze-failures"></a>分析故障
Application Insights 收集应用程序中出现的所有故障，让用户能够查看其在不同操作中出现的频率，帮助将精力集中在具有最大影响的故障上。  用户可随后深入探索这些故障的详细信息，从而确定根本原因。   

1. 选择“Application Insights”，然后选择订阅  。  
2. 若要打开“故障”面板，请选择“调查”菜单下的“故障”，或单击“失败的请求”图表。

    ![失败的请求](media/tutorial-runtime-exceptions/failed-requests.png)

3. “失败的请求”  面板将为应用程序的每个操作显示失败请求计数和受影响用户的数量。  通过按用户对此类信息排序，可确定对用户具有最大影响的故障。  在此示例中，**GET Employees/Create** 和 **GET Customers/Details** 是有待调查的可能候选对象，因为其拥有大量失败的请求和受影响的用户。  选择操作后，可在右侧面板中显示有关此操作的详细信息。

    ![“失败的请求”面板](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. 缩短时间范围，详细查看故障率出现峰值的时段。

    ![“失败的请求”窗口](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. 单击包含所筛选结果数目的按钮即可查看相关的示例。 “建议的”示例有来自所有组件的相关遥测数据，即使采用实际上是在其中随机完成的。 单击搜索结果即可查看有关失败的详细信息。

    ![失败请求示例](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. 失败请求的详细信息显示了甘特图，该图显示此事务中有两个依赖项失败，这两个失败所用时间占事务总持续时间的 50% 以上。 此体验提供的所有遥测跨分布式应用程序的多个组件，这些组件与此操作 ID 相关。 [详细了解此新体验](../../azure-monitor/app/transaction-diagnostics.md)。 可以选择任意项，然后即可在右侧查看其详细信息。 

    ![失败请求的详细信息](media/tutorial-runtime-exceptions/failed-request-details.png)

7. 操作详细信息还显示可能是故障原因的 FormatException。  此时，可发现这是无效邮政编码导致的。 可以打开调试快照，然后即可在 Visual Studio 中查看代码级别的调试信息。

    ![异常详细信息](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>确定故障代码
Snapshot Debugger 收集应用程序中最频繁出现的异常的快照，帮助在生产中诊断其根本原因。  可在门户中查看调试快照，查看调用堆栈并检查每个调用堆栈帧中的变量。 之后，可以选择通过下载快照并在 Visual Studio 2019 Enterprise 中打开它来调试源代码。

1. 在异常的属性中，单击“打开调试快照”  。
2. 此时会打开“调试快照”  面板，并显示请求的调用堆栈。  单击任意方法来查看提交请求时所有局部变量的值。  从此示例的第一个方法开始，可发现局部变量没有值。

    ![调试快照](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. 具有有效值的第一个调用是 **ValidZipCode**，可发现邮政编码通过无法转换为整数的字母提供。  这是代码中的错误，需要更正。

    ![调试快照](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. 然后，可以选择将此快照下载到 Visual Studio 中，我们可在其中找到需要更正的实际代码。 为此，请单击“下载快照”  。
5. 快照会加载到 Visual Studio 中。
6. 现在可在 Visual Studio Enterprise 中运行调试会话，以便快速确定导致异常的代码行。

    ![代码中的异常](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>使用分析数据
Application Insights 收集的所有数据都存储在 Azure Log Analytics 中，Azure Log Analytics 提供一种丰富查询语言，方便使用各种方式分析数据。  我们可使用此数据来分析生成了我们正在研究的异常的请求。 

1. 单击代码上方的 CodeLens 信息，查看 Application Insights 提供的遥测数据。

    ![代码](media/tutorial-runtime-exceptions/codelens.png)

1. 单击“分析影响”  ，打开 Application Insights Analytics。  它使用多个提供失败请求详细信息（比如受影响的用户、浏览器和区域）的查询填充。<br><br>![分析](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>添加工作项
如果将 Application Insights 连接到跟踪系统（比如 Azure DevOps 或 GitHub），可直接通过 Application Insights 创建工作项。

1. 返回 Application Insights 中的“异常属性”  面板。
2. 单击“新建工作项”  。
3. “新建工作项”  面板将打开，其中已填充有关异常的详细信息。  可在保存前添加任何其他信息。

    ![新建工作项](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>后续步骤
现在，你已学会如何确定运行时异常，请继续完成下一教程，学习如何确定和诊断性能问题。

> [!div class="nextstepaction"]
> [确定性能问题](../../azure-monitor/learn/tutorial-performance.md)
