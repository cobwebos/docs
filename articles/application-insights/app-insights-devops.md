---
title: "Web 应用程序性能监视 - Azure Application Insights | Microsoft Docs"
description: "Application Insights 如何适应 devOps 周期"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6bc820657da4a72455d76f38dbdb06fe50b0c895
ms.lasthandoff: 04/12/2017


---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>使用 Application Insights 对 Web 应用和服务进行深度诊断
## <a name="why-do-i-need-application-insights"></a>为什么需要 Application Insights？
Application Insights 可以监视正在运行的 Web 应用。 它可以告知故障和性能方面的问题，帮助分析客户如何使用你的应用。 它适用于许多平台（ASP.NET、J2EE、Node.js ...）上运行的应用，可以托管在云中或本地。 

![传递 Web 应用时面临的复杂性](./media/app-insights-devops/010.png)

监视正在运行的现代应用程序至关重要。 最重要的是，我们需要在大多数客户检测到故障之前，先行检测到这些故障。 此外，我们还需要发现并修复性能问题，尽管这些问题不是灾难性的，但也许会使某些任务的速度变慢，或对用户带来不便。 如果你对系统的性能感到满意，可能想要知道用户正使用系统执行哪些操作：他们在使用最新功能吗？ 是否已成功使用？

现代 Web 应用程序是在连续传递周期中开发的：发布新功能或改进功能；观察应用程序对用户的适用程度；根据这种了解规划下一个增量开发。 此周期的关键部分是观察阶段。 Application Insights 提供用于监视 Web 应用程序性能和使用情况的工具。

此过程的最重要方面是诊断。 如果应用程序失败，将造成业务损失。 因此，监视框架的主要作用是可靠检测故障、立即发出通知，并提供诊断问题所需的信息。 这正是 Application Insights 的作用。

### <a name="where-do-bugs-come-from"></a>Bug 来自何处？
Web 系统中的故障通常由配置问题或系统中许多组件之间的交互不当所造成。 因此，处理实时站点事件时的首要任务就是找到问题所在：原因出自哪些组件或关系？

有点年纪的那一代人可能还记得一个计算机程序只能在一台计算机中运行的“单纯”岁月。 开发人员在交付之前彻底测试该程序，但交付之后，就很少再看到或听到任何有关该程序的事。 用户必须忍受残留的 Bug 长达数年之久。 

现在，情况已截然不同。 应用需要在许多不同的设备上运行，很难保证应用在每台设备上都有完全相同的行为。 在云中托管应用程序，意味着 Bug 可以很快修复，但同时也意味着竞争在持续进行，可以预料到新功能会频繁推出。 

在这种情况下，严格控制 Bug 计数的唯一方法是将单元测试自动化。 无法在每次交付时手动重新测试所有功能。 单元测试现已成功构建过程的常见组成部分。 Xamarin Test Cloud 等工具可通过在多个浏览器版本中提供自动化 UI 测试来提供帮助。 这些测试方案使我们有望将应用中的 Bug 比率降到最低。

典型的 Web 应用程序有许多实时组件。 除了客户端（在浏览器或设备应用程序中）和 Web 服务器以外，还可能是实质的后端处理。 后端相当于组件的管道，或者松散的协作项目集合。 此外，其中的许多组件都不受你的控制 - 它们是所需的外部服务。

在此类配置中，除非在实时系统本身内部，否则很难以经济节省的方式测试或预见每种可能的故障模式。 

### <a name="questions-"></a>问题...
开发 Web 系统时，我们可能会自我提出这样的问题：

* 应用崩溃了吗？ 
* 到底发生了什么情况？ - 如果请求失败，我想要知道问题出在哪里。 我们需要跟踪事件...
* 应用速度够快吗？ 响应典型的请求需要多长时间？
* 服务器可以处理负载吗？ 当请求速率增加时，可以保持稳定的响应时间吗？
* 依赖项  - REST API、数据库以及应用调用的其他组件 - 的响应能力如何？ 具体而言，系统变慢是组件造成的，还是因为其他人的响应速度较慢？
* 应用是否正在运行？ 全世界都可以看到它吗？ 让我知道它是否已停止...
* 根本原因是什么？ 故障源自我的组件还是依赖项？ 是通信问题造成的吗？
* 有多少用户受到影响？ 如果有多个问题要解决，哪个问题最重要？

## <a name="what-is-application-insights"></a>什么是 Application Insights？
![Application Insights 的基本工作流](./media/app-insights-devops/020.png)

1. Application Insights 可检测应用，并在应用运行时发送相关遥测数据。 可以在应用中构建 Application Insights SDK，或者在运行时应用检测。 前一种方法更有弹性，因为你可以将自己的遥测功能添加到普通模块。
2. 遥测数据将发送到 Application Insights 门户，并在其中存储和处理。 （尽管 Application Insights 托管在 Microsoft Azure 中，但它可以监视任何 Web 应用，而不只是 Azure 应用）。
3. 遥测数据以事件图表和表格的形式呈现。

有两种主要类型的遥测数据：聚合实例和原始实例。 

* 例如，实例数据包含 Web 应用收到的请求报告。 可以使用 Application Insights 门户中的搜索工具查找和检查请求的详细信息。 实例包含的数据如下：应用要花费多少时间来响应请求，以及请求的 URL、客户端的大约位置和其他数据。
* 聚合数据包含每个单位时间的事件计数，因此可将请求速率与响应时间进行比较。 它还包含请求响应时间等指标平均值。

数据的主要类别如下：

* 对应用的请求（通常是 HTTP 请求），其中包含有关 URL、响应时间以及成功或失败结果的数据。
* 依赖项 - 应用执行的 REST 和 SQL 调用，也包含 URI、响应时间和成功结果
* 异常，包括堆栈跟踪。
* 来自用户浏览器的页面视图数据。
* 性能计数器等指标，以及你自行编写的指标。 
* 可用于跟踪业务事件的自定义事件
* 用于调试的日志跟踪。

## <a name="case-study-real-madrid-fc"></a>案例研究：皇家马德里足球俱乐部
[皇家马德里足球俱乐部](http://www.realmadrid.com/)的 Web 服务为全球各地大约 4.5 亿球迷提供服务。 球迷可通过 Web 浏览器和俱乐部的移动应用来访问此服务。 球迷不仅可以预订门票，还能访问比赛结果、球员以及即将举行的比赛相关信息和视频剪辑。 他们可以使用进球数等筛选条件进行搜索。 另外，该服务还提供社交媒体链接。 用户体验已高度个性化，旨在增进球迷的双向沟通。

该解决方案是 [Microsoft Azure 上的一个服务与应用程序系统](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx)。 可伸缩性是关键要求：流量变化无常，可能在比赛期间和接近比赛时达到高峰。

对于皇家马德里而言，监视系统性能非常重要。 Visual Studio Application Insights 提供整个系统的完整视图，确保提供可靠的高级服务。 

该俱乐部还能对球迷进行详细了解：他们的所在位置（只有 3% 在西班牙）、他们对球员、历史结果和即将举行的比赛的兴趣程度，以及对比赛结果的反应。

其中的大多数遥测数据是自动收集的，无需添加任何代码，因而简化了解决方案，降低了操作复杂性。  对于皇家马德里而言，Application Insights 每个月可处理 38 亿个遥测点。

皇家马德里使用 Power BI 模块查看其遥测数据。

![Application Insights 遥测的 Power BI 视图](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>智能检测
[主动诊断](app-insights-proactive-diagnostics.md)是一项最新功能。 无需提供任何特殊配置，Application Insights 就能自动检测并提醒应用中出现异常的故障率提高情况。 它的智能程度高到可以忽略偶发故障的背景，以及根据请求数的增加按比率增加的故障。 例如，如果依赖的某个服务发生故障，或者刚刚部署的新版本没有运行得很好，则查看电子邮件之后立即就能了解相关信息。 （它还提供 Webhook 来触发其他应用。）

此功能的另一个方面是针对遥测数据执行每日深入分析，以便找到很难发现的异常性能模式。 例如，它会查找与特定地理区域或特定浏览器版本相关的性能变慢问题。

在这两种情况下，警报不仅仅通知它发现的症状，而且还提供所需的数据来帮助诊断问题，例如相关的异常报告。

![主动诊断发送的电子邮件](./media/app-insights-devops/030.png)

客户 Samtec 说：“在最近的功能过渡期间，我们发现一个小型数据库已到达它的资源限制并导致超时。 当我们分类问题时，主动检测警报立即显现，其速度就像宣传的那样接近实时。 这种与 Azure 平台结合的警报几乎帮助我们瞬间解决了问题。 总停机时间不到 10 分钟。”

## <a name="live-metrics-stream"></a>实时指标流
部署最新版本是令人焦虑的体验。 如果出现任何问题，我们希望立即了解情况，以便在必要时取消部署。 实时指标流以大约 1 秒的延迟提供关键指标。

![实时指标](./media/app-insights-devops/040.png)

可让你立即检查任何故障或异常的样本。

![实时故障事件](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>应用程序地图
应用程序地图可自动发现应用程序拓扑并呈现其上的性能信息，让你轻松地在整个分布式环境中识别性能瓶颈和有问题的流程。 此外，还可以发现 Azure 服务上的应用程序依赖项。 可以通过了解问题是与代码相关还是与依赖项相关并从单个位置深入到相关诊断体验来将问题分类。 例如，应用程序可能由于 SQL 层的性能降低而发生故障。 使用应用程序地图可以立即看到这种故障，深入到 SQL 索引顾问或 Query Insights 体验。

![应用程序地图](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
借助 [Analytics](app-insights-analytics.md) 可以使用类似于 SQL 的强大语言编写任意查询。  将各种不同的观点链接到一起后，跨整个应用程序堆栈的诊断就会变得很容易。可以提出正确的问题，将服务性能与业务指标和客户体验相关联。 

可以查询门户中存储的所有遥测实例和指标原始数据。 语言包括筛选、联接、聚合与其他操作。 可以计算字段并执行统计分析。 目前提供表格式和图形可视化效果。

![分析查询和结果图表](./media/app-insights-devops/025.png)

例如，可以轻松执行以下操作：

* 按客户层将应用程序的请求性能数据分段，以了解客户的体验。
* 在实时站点调查期间，搜索特定的错误代码或自定义的事件名称。
* 深入到特定客户的应用使用情况，了解功能的获取与采用情况。
* 跟踪特定用户的会话与响应时间，使支持与运营团队能够即时提供客户支持。
* 确定常用的应用功能，以便能够解答功能优先问题。

客户 DNN 说：“Application Insights 为我们提供了方程式中遗漏的部分，使我们能够根据需要合并、排序、查询及筛选数据。 我们的团队可以使用自己的创意和体验，通过功能强大的查询语言来查找数据，得到相关见解，解决我们甚至都不知道发生了的问题。 很多有意思的回答都是针对开头为‘我想知道，如果...’这样的问题。”

## <a name="development-tools-integration"></a>开发工具集成
### <a name="configuring-application-insights"></a>配置 Application Insights
Visual Studio 和 Eclipse 提供相应的工具来为开发中的项目配置正确的 SDK 包。 有一个菜单命令可以添加 Application Insights。

如果你正在使用  Log4N、NLog 或 System.Diagnostics.Trace 等跟踪日志记录框架，可以选择将记录发送到 Application Insights 以及另一个遥测系统，轻松地将跟踪与请求、依赖项调用和异常相关联。

### <a name="search-telemetry-in-visual-studio"></a>在 Visual Studio 中搜索遥测数据
开发和调试某个功能时，可以使用 Web 门户中的相同搜索工具在 Visual Studio 中直接查看和搜索遥测数据。

此外，当 Application Insights 记录异常时，你可以在 Visual Studio 中查看数据点，并直接跳转到相关代码。

![Visual Studio 搜索](./media/app-insights-devops/060.png)

在调试期间，可以选择将遥测数据保留在开发计算机中，并在 Visual Studio 中查看这些数据，而无需将其发送到门户。 这个本地选项可避免将调试遥测数据与生产遥测数据混合使用。

### <a name="build-annotations"></a>版本批注
如果使用 Visual Studio Team Services 构建和部署应用，部署批注将显示在门户中的图表上。 如果最新版本对指标产生了任何影响，这种影响会变得很明显。

![版本批注](./media/app-insights-devops/070.png)

### <a name="work-items"></a>工作项
引发警报后，Application Insights 可以在工作跟踪系统中自动创建工作项。

## <a name="but-what-about"></a>但是...？
* [隐私和存储](app-insights-data-retention-privacy.md) - 遥测数据保留在 Azure 安全服务器上。
* 性能 - 影响很小。 遥测数据将经过批处理。
* [价格](app-insights-pricing.md) - 可以从免费版着手，如果用量不大，可以一直使用免费版。


## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤
Application Insights 入门非常简单。 主要选项包括：

* 检测已在运行的 Web 应用。 这样就可以体验到所有内置的性能遥测功能。 该功能适用于 [Java](app-insights-java-live.md) 和 [IIS 服务器](app-insights-monitor-performance-live-website-now.md)，以及 [Azure Web 应用](app-insights-azure.md)。
* 在开发期间检测项目。 可以针对 [ASP.NET](app-insights-asp-net.md) 或 [Java](app-insights-java-get-started.md) 应用以及 [Node.js](app-insights-nodejs.md) 和很多[其他类型](app-insights-platforms.md)的应用执行此操作。 
* 通过添加简短的代码片段来检测[任何网页](app-insights-javascript.md)。


