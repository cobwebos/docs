---
title: 使用 Application Insights Profiler 探查 Azure 上的实时 Web 应用 | Microsoft Docs
description: 使用一个精简的探查器识别 Web 服务器代码中的热路径。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 6048a17bf50ecac691c7cf687f87e454c54ee9d9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521877"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure Web 应用

Azure Application Insights 的此功能已分别面向 Azure 应用服务的 Web 应用功能和 Azure 计算资源提供正式版和预览版。 有关[在本地使用 Profiler](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers) 的信息。

本文介绍了使用 [Application Insights](app-insights-overview.md) 时在实时 Web 应用程序的每个方法上花费的时间。 Application Insights Profiler 工具显示了由应用处理的实时请求的详细配置文件。 Profiler 突出显示使用时间最多的热路径。 将会根据采样分析响应时间各不相同的请求。 可以通过各种技术尽量降低与应用程序相关联的开销。

Profiler 目前适用于 Web 应用上运行的 ASP.NET 和 ASP.NET Core Web 应用。 若要使用 Profiler，“基本”服务层或更高层是必需的。

## <a id="installation"></a> 为 Web 应用启用 Profiler

在部署 Web 应用后，无论是否在源代码中包括了 App Insights SDK，都要执行以下操作：

1. 转到 Azure 门户中的“应用服务”窗格。
1. 导航到“设置”>“监视”窗格。

   ![在应用服务门户上启用 App Insights](./media/app-insights-profiler/AppInsights-AppServices.png)

1. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视 Web 应用。 接受所有默认选项。 默认情况下会启用**代码级诊断**，并且它会启用 Profiler。

   ![添加 App Insights 站点扩展][Enablement UI]

1. Profiler 现在已随 App Insights 站点扩展安装，并且使用应用服务应用设置启用。

    ![Profiler 的应用设置][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>为 Azure 计算资源启用 Profiler（预览）

有关信息，请参阅[用于 Azure 计算资源的 Profiler 的预览版本](https://go.microsoft.com/fwlink/?linkid=848155)。

## <a name="view-profiler-data"></a>查看探查器数据

确保应用程序可以接收流量。 如果正在进行试验，可以通过 [Application Insights 性能测试](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)生成针对 Web 应用的请求。 如果最近启用了 Profiler，则可以运行简短的负载测试大约 15 分钟，这样就会生成探查器跟踪。 如果已启用 Profiler 相当长一段时间，请记住，Profiler 每小时会随机运行两次，每次运行两分钟。 建议运行负载测试一个小时，确保获取探查器跟踪样本。

应用程序收到一些流量后，请转到“性能”窗格，选择“采取措施”，以查看 Profiler 跟踪，然后选择“Profiler 跟踪”按钮。

![“Application Insights 性能”窗格预览 Profiler 跟踪][performance-blade-v2-examples]

选择一个样本可以显示执行该请求所花费时间的代码级细节。

![Application Insights 跟踪浏览器][trace-explorer]

跟踪浏览器会显示以下信息：

* **显示热路径**：打开最大的叶节点，或者至少打开某个接近的节点。 大多数情况下，此节点与性能瓶颈相邻。
* **标签**：函数或事件的名称。 树中显示了代码与所发生事件（如 SQL 和 HTTP 事件）的混合形式。 最前面的事件表示请求总持续时间。
* **所用时间**：从操作开始到操作结束之间的时间间隔。
* **时间**：运行函数或事件的时间，此值相对于运行其他函数的时间。

## <a name="how-to-read-performance-data"></a>如何读取性能数据

Microsoft 服务探查器结合使用采样方法和检测来分析应用程序的性能。 当详细收集操作正在进行时，服务探查器将每隔一毫秒对每台计算机的 CPU 指令指针采样。 每个样本捕获当前正在执行的线程的完整调用堆栈。 它从高级抽象化和低级抽象化两个角度，提供有关线程当前行为的有用详细信息。 服务探查器还会收集其他事件（包括上下文切换事件、任务并行库 (TPL) 事件和线程池事件），以跟踪活动相关性和因果关系。

时间线视图中显示的调用堆栈是采样和检测的结果。 由于每个样本会捕获线程的整个调用堆栈，因此包含 Microsoft .NET Framework 中的代码，以及引用的其他框架中的代码。

### <a id="jitnewobj"></a>对象分配（clr!JIT\_New 或 clr!JIT\_Newarr1）

clr!JIT\_New 和 clr!JIT\_Newarr1 是 .NET Framework 中的 helper 函数，用于分配托管堆中的内存。 分配对象时，将调用 clr!JIT\_New。 分配对象数组时，将调用 clr!JIT\_Newarr1。 这两个函数通常速度很快，花费的时间相对较短。 如果发现时间线中的 clr!JIT\_New 或 clr!JIT\_Newarr1 花费了很长时间，则可能表示代码分配了很多对象，从而消耗了大量的内存。

### <a id="theprestub"></a>加载代码 (clr!ThePreStub)

clr!ThePreStub 是 .NET Framework 中的 helper 函数，用于准备首次执行的代码。 这通常包括但不限于恰时 (JIT) 编译。 对于每个 C# 方法，在进程的生存期内，最多只应调用 clr!ThePreStub 一次。

如果 clr!ThePreStub 针对某个请求花费了很长时间，则表示这是第一个执行该方法的请求。 .NET Framework 运行时加载该方法的时间非常重要。 可以考虑在用户访问该代码部分之前使用执行该代码部分的预热进程，或者考虑在程序集中运行本机映像生成器 (ngen.exe)。

### <a id="lockcontention"></a>锁争用（clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker）

clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker 指示当前线程正在等待释放锁。 执行 C# LOCK 语句、调用 Monitor.Enter 方法或者结合 MethodImplOptions.Synchronized 属性调用某个方法时，通常会显示此文本。 如果线程 _A_ 获取了某个锁，而线程 _B_ 在线程 _A_ 释放该锁之前尝试获取同一个锁，此时通常会发生锁争用。

### <a id="ngencold"></a>加载代码 ([COLD])

如果方法名称包含 [COLD]（例如 mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined），则表示 .NET Framework 运行时首次执行的代码未经过<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">按配置优化</a>功能的优化。 对于每个方法，在进程的生存期内，它最多只应显示一次。

如果针对某个请求加载代码花费的时间很长，这表示这是第一个执行该方法的未优化部分的请求。 请考虑在用户访问该代码部分之前使用执行该代码部分的预热进程。

### <a id="httpclientsend"></a>发送 HTTP 请求

HttpClient.Send 等方法指示代码正在等待某个 HTTP 请求完成。

### <a id="sqlcommand"></a>数据库操作

SqlCommand.Execute 等方法指示代码正在等待某个数据库操作完成。

### <a id="await"></a>等待 (AWAIT\_TIME)

AWAIT\_TIME 指示代码正在等待另一个任务完成。 这种情况通常发生在 C# AWAIT 语句上。 当代码执行 C# AWAIT 时，线程会回退并将控制权返回给线程池，此时，不会有任何阻塞的线程等待 AWAIT 完成。 但是，从逻辑上讲，执行 AWAIT 的线程会被“阻塞”并等待该操作完成。 AWAIT\_TIME 语句指示等待任务完成的阻塞时间。

### <a id="block"></a>阻塞时间

BLOCKED_TIME 指示代码正在等待另一个资源变为可用。 例如，它可能会等待同步对象或线程变为可用，或等待请求完成。

### <a id="cpu"></a>CPU 时间

CPU 正忙于执行指令。

### <a id="disk"></a>磁盘时间

应用程序正在执行磁盘操作。

### <a id="network"></a>网络时间

应用程序正在执行网络操作。

### <a id="when"></a>“时间”列

“时间”列是针对节点收集的非独占样本在各个时间发生的变化的可视化效果。 请求的总范围划分成 32 个时间存储桶。 该节点的非独占样本会在这 32 个存储桶中累积。 每个存储桶用一个条形表示。 条形的高度表示缩放后的值。 如果节点带有 CPU_TIME 或 BLOCKED_TIME 标记，或者跟资源（例如，CPU、磁盘、线程）的消耗存在某种明显关系，则条形表示在该 Bucket 的时间段内消耗了其中的某个资源。 如果消耗多个资源，这些指标的值可能大于 100%。 例如，如果在某个时间间隔内平均使用两个 CPU，则指标值为 200%。

## <a name="limitations"></a>限制

默认数据保留期为 5 天。 每天引入的最大数据量为 10 GB。

使用 Profiler 服务没有任何费用。 若要使用 Profiler 服务，Web 应用必须至少托管在 Web 应用的“基本”层中。

## <a name="overhead-and-sampling-algorithm"></a>开销和采样算法

在托管的应用程序已启用 Profiler 来捕获跟踪的每个虚拟机上，Profiler 每小时随机运行 2 分钟。 Profiler 在运行时会给服务器增添 5% 到 15% 的 CPU 开销。

可用于托管应用程序的服务器越多，Profiler 对总体应用程序性能的影响越小。 这是因为采样算法会使 Profiler 在任何时候都只在 5% 的服务器上运行。 有更多服务器可用于处理 Web 请求，以抵消运行 Profiler 所产生的服务器开销。

## <a name="disable-profiler"></a>禁用 Profiler

若要为单个 Web 应用实例停止或重启 Profiler，请在“Web 作业”下转到“Web 应用资源”。 若要删除 Profiler，请转到“扩展”。

![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有 Web 应用上都启用 Profiler，以便尽早发现任何性能问题。

如果使用 WebDeploy 将更改部署到 Web 应用程序，请确保排除 App_Data 文件夹，以防在部署期间将它删除。 否则，下一次将 Web 应用程序部署到 Azure 时，会删除 Profiler 扩展文件。


## <a id="troubleshooting"></a>故障排除

### <a name="too-many-active-profiling-sessions"></a>活动分析会话太多

目前，最多可以对同一服务计划中运行的 4 个 Azure Web 应用和部署槽启用 Profiler。 如果 Profiler Web 作业报告活动探查会话太多，请将一些 Web 应用移到另一服务计划。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何确定 Application Insights Profiler 是否正在运行？

Profiler 在 Web 应用中以连续 Web 作业的形式运行。 可以在 [Azure 门户](https://portal.azure.com)中打开 Web 应用资源。 在“WebJobs”窗格中，查看 ApplicationInsightsProfiler 的状态。 如果探查器未运行，请打开“日志”获取详细信息。

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>为何运行 Profiler 后仍找不到任何堆栈示例？

下面是可以检查的几个因素：

* 确保 Web 应用服务计划属于“基本”层或更高层。
* 确保 Web 应用中已启用 Application Insights SDK 2.2 Beta 或更高版本。
* 确保配置 Web 应用中的 APPINSIGHTS_INSTRUMENTATIONKEY 设置所采用的检测密钥与 Application Insights SDK 使用的检测密钥相同。
* 确保 Web 应用在 .NET Framework 4.6 上运行。
* 如果 Web 应用是 ASP.NET Core 应用程序，则必须至少运行 ASP.NET Core 2.0。

启动 Profiler 后，将有短暂的一段预热时间，在此期间，Profiler 会积极收集多种性能跟踪。 此后，Profiler 会每隔一小时收集性能跟踪两分钟。

> [!NOTE]
> 探查器代理中有一个 bug，使其无法上传从 ASP.NET Core 2.1 上运行的应用程序中获取的跟踪。 我们正在努力修复，很快就会解决。

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>使用 Azure 服务探查器时出现问题， 原因是什么？

启用 Application Insights Profiler 时，会禁用 Azure 服务探查器代理。

### <a id="double-counting"></a>并行线程的重复计算

在某些情况下，堆栈查看器中的总时间指标大于请求的持续时间。

如果存在与请求关联的两个或更多个线程并且它们正并行运行，则可能会发生这种情况。 在这种情况下，总线程时间就会超过已用时间。 一个线程可能会等待另一个线程完成。 查看器会尝试检测此数据并省略不相关的等待时间，但是，它会倾向于显示过多信息，而不是省略关键信息。

如果看到跟踪中出现并行线程，请确定哪些线程处于等待状态，以便可以确定请求的关键路径。 在大多数情况下，快速进入等待状态的线程只是在等待其他线程完成。 请专注于其他线程，忽略等待中线程花费的时间。

### <a id="issue-loading-trace-in-viewer"></a>无探查数据

下面是可以检查的几个因素：

* 如果要查看的数据的期限超过了好几周，请尝试限制时间筛选器并重试。
* 确保代理或防火墙未阻止对 https://gateway.azureserviceprofiler.net 的访问。
* 确保在应用中使用的 Application Insights 检测密钥与用来启用探查的 Application Insights 资源使用的检测密钥相同。 密钥通常在 ApplicationInsights.config 文件中，但也可能在 web.config 或 app.config 文件中。

### <a name="error-report-in-the-profiling-viewer"></a>探查查看器中的错误报告

在门户中提交支持票证。 请务必包含错误消息中的相关性 ID。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署错误：目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”

如果在已启用 Profiler 的情况下将 Web 应用重新部署到 Web 应用资源，可能会看到如下消息：

*目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”*

如果通过脚本或通过 Visual Studio Team Services 部署管道运行 Web 部署，则会发生此错误。 解决方法是将以下附加部署参数添加到 Web 部署任务：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

这些参数将删除 Application Insights Profiler 所用的文件夹，并取消阻止重新部署进程。 它们不影响当前运行的 Profiler 实例。

## <a name="manual-installation"></a>手动安装

配置 Profiler 时，将对 Web 应用的设置进行更新。 如果你的环境有此要求，则可以手动应用更新。 例如，应用程序在适用于 PowerApps 的 Web 应用环境中运行。

1. 在“Web 应用控制”窗格中，打开“设置”。
1. 将“.Net Framework 版本”设置为“v4.6”。
1. 将“Always On”设置为“打开”。
1. 添加应用设置“APPINSIGHTS_INSTRUMENTATIONKEY”，将值设置为 SDK 使用的同一检测密钥。
1. 打开“高级工具”。
1. 选择“转到”打开 Kudu 网站。
1. 在 Kudu 网站上，选择“站点扩展”。
1. 从 Azure Web 应用库中安装“Application Insights”。
1. 重新启动 Web 应用。

## <a id="profileondemand"></a> 手动触发 Profiler

可以通过单击一个按钮手动触发 Profiler。 假设你在运行 Web 性能测试。 你将需要使用跟踪来帮助你了解 Web 应用在负载下的性能。 控制何时捕获跟踪至关重要，因为虽然你知道负载测试将在何时运行，但随机采样时间间隔可能会错过它。
以下步骤展示了此方案如何工作：

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>（可选）步骤 1：通过启动 Web 性能测试生成发往 Web 应用的流量

如果 Web 应用已有传入流量或者你就是希望手动生成流量，请跳过本部分并前进到步骤 2。

导航到 Application Insights 门户中的“配置”>“性能测试”。 单击“新建”按钮以启动新的性能测试。
![创建新的性能测试][create-performance-test]

在“新建性能测试”窗格中，配置测试目标 URL。 接受所有默认设置并开始运行负载测试。

![配置负载测试][configure-performance-test]

你将看到新测试的状态最先为“已排队”，随后状态变为“正在进行”。

![负载测试已提交并排队][load-test-queued]

![负载测试正在运行中][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>步骤 2：按需启动 Profiler

在负载测试开始运行后，可以启动 Profiler 以在 Web 应用接收负载期间捕获该应用上的跟踪。
导航到“配置 Profiler”窗格：

![“配置 Profiler”窗格条目][configure-profiler-entry]

在“配置 Profiler”窗格中，有一个“立即探查”按钮可用来在链接的 Web 应用的所有实例上触发 Profiler。 此外，还可以查看 Profiler 在过去什么时间处于运行状态。

![按需 Profiler][profiler-on-demand]

你将看到有关 Profiler 运行状态的通知和状态变化。

### <a name="step-3-view-traces"></a>步骤 3：查看跟踪

在 Profiler 完成运行后，按照通知中的说明转到“性能”边栏选项卡并查看跟踪。

### <a name="troubleshooting-on-demand-profiler"></a>对按需 Profiler 进行故障排除

有时，在按需会话后可能会看到 Profiler 超时错误消息：

![Profiler 超时错误][profiler-timeout]

有两个原因可能会导致你看到此错误：

1. 按需 Profiler 会话已成功，但 Application Insights 花费了较长的时间来处理所收集的数据。 如果数据在 15 分钟内未完成处理，则门户将显示一条超时消息。 但是，片刻之后将显示 Profiler 跟踪。 如果发生此情况，目前请直接忽略错误消息。 我们正在积极开发修补程序。

1. Web 应用有一个不具备按需功能的较旧版本的 Profiler 代理。 如果之前启用了 Application Insights Profiler，则可能需要更新 Profiler 代理以开始使用按需功能。
  
请执行以下步骤来检查并安装最新的 Profiler：

1. 转到应用服务的“应用设置”并检查是否设置了以下设置：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0 如果这些设置有任何一个未设置，请转到 Application Insights 启用窗格来安装最新的站点扩展。

1. 转到应用服务门户中的 Application Insights 窗格。

    ![从应用服务门户中启用 Application Insights][enable-app-insights]

1. 如果在下面的页面中看到了“更新”按钮，请单击它以更新 Application Insights 站点扩展，这将安装最新的 Profiler 代理。
![更新站点扩展][update-site-extension]

1. 然后单击“更改”来确保启用 Profiler，并选择“确定”来保存更改。

    ![更改并保存 App Insights][change-and-save-appinsights]

1. 返回到应用服务的“应用设置”选项卡来复查是否已设置了以下应用设置项：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

    ![Profiler 的应用设置][app-settings-for-profiler]

1. （可选）检查扩展版本，并确保没有任何可用的更新。

    ![检查是否有扩展更新][check-for-extension-update]

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
