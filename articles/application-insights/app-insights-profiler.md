---
title: "使用 Application Insights Profiler 探查 Azure 上的实时 Web 应用 | Microsoft Docs"
description: "使用一个精简的探查器识别 Web 服务器代码中的热路径。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: f8ba1a6308dfe234fff700d363fb9252b94570e2
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure Web 应用

Application Insights 的此功能已分别面向 Azure 应用服务和 Azure 计算资源提供正式版和预览版。

使用 [Application Insights Profiler](app-insights-overview.md) 确定实时 Web 应用程序中的每个方法花费了多长时间。 Application Insights 探查工具会显示应用提供的实时请求的详细探查信息，并突出显示占用最多时间的“热路径”。 该探查器会自动选择具有不同响应时间的示例，然后使用各种技术将开销降到最低。

该探查器目前适用于 Azure 应用服务上运行的、至少处于基本服务层的 ASP.NET Web 应用。

## <a id="installation"></a>启用探查器

在代码中[安装 Application Insights](app-insights-asp-net.md)。 如果已安装，请确保使用的是最新版本。 要检查最新版本，请在解决方案资源管理器中右键单击你的项目，然后选择“管理 NuGet 包” > “更新” > “更新所有包”。 然后重新部署应用。

*使用的是 ASP.NET Core？获取[详细信息](#aspnetcore)。*

在 [Azure 门户](https://portal.azure.com)中，打开 Web 应用的 Application Insights 资源。 选择“性能” > “启用 Application Insights Profiler”。

![选择“启用探查器”横幅][enable-profiler-banner]

另外，可以通过选择“配置”来查看状态，启用或禁用探查器。

![在“性能”下，选择“配置”][performance-blade]

配置有 Application Insights 的 Web 应用会在“配置”下列出。 如果需要，请根据说明安装探查器代理。 如果尚无 Web 应用配置有 Application Insights，请选择“添加链接应用”。

若要控制链接的所有 Web 应用上的探查器，请在“配置”窗格中选择“启用探查器”或“禁用探查器”按钮。

![“配置”窗格选项][linked app services]

与通过应用服务计划托管的 Web 应用不同，在 Azure 计算资源（例如：Azure 虚拟机、虚拟机规模集、Azure Service Fabric 或 Azure 云服务）中托管的应用程序不由 Azure 直接管理。 在这种情况下，将没有任何要链接到的 Web 应用。 请改为选择“启用探查器”按钮，而不是链接到应用。

## <a name="disable-the-profiler"></a>禁用探查器
若要为单个应用服务实例停止或重新启动探查器，可在“Web 作业”下转到“应用服务资源”。 若要删除探查器，请转到“扩展”。

![为 Web 作业禁用探查器][disable-profiler-webjob]

建议在所有 Web 应用上都启用探查器以便尽早发现任何性能问题。

如果使用 WebDeploy 将更改部署到 Web 应用程序，请确保排除 App_Data 文件夹，以防在部署期间将它删除。 否则，下一次将 Web 应用程序部署到 Azure 时，会删除探查器扩展文件。

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>将探查器与 Azure VM 和 Azure 计算资源一起使用（预览版）

[在运行时为 Azure 应用服务启用 Application Insights](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights) 后，Application Insights Profiler 会自动可用。 如果已经为资源启用了 Application Insights，则可能需要通过使用“配置”向导更新到最新版本。

获取有关[用于 Azure 计算资源的探查器的预览版本](https://go.microsoft.com/fwlink/?linkid=848155)的信息。


## <a name="limitations"></a>限制

默认数据保留期为 5 天。 每天引入的最大数据量为 10 GB。

使用探查器服务没有任何费用。 要使用探查器服务，Web 应用必须至少托管在应用服务的基本层中。

## <a name="overhead-and-sampling-algorithm"></a>开销和采样算法

在托管已启用探查器以捕获跟踪的应用程序的每个虚拟机上，探查器每小时随机运行 2 分钟。 当探查器运行时，它使服务器增加 5% 到 15% 的 CPU 开销。
可用于托管应用程序的服务器越多，探查器对总体应用程序性能的影响越小。 这是因为采样算法会使探查器在任何时候都只在 5% 的服务器上运行。 有更多服务器可用于响应 Web 请求，以抵消运行探查器所产生的服务器开销。


## <a name="view-profiler-data"></a>查看探查器数据

转到“性能”窗格，然后向下滚动到操作列表。

![“Application Insights 性能”窗格示例列][performance-blade-examples]

操作表包含以下列：

* **计数**：在“计数”窗格的时间范围内这些请求的数目。
* **中值**：应用响应请求所花费的平均时间。 在所有响应中，有一半的响应速度超过此值。
* **95%**：95% 的响应速度超过此值。 如果此值与中值有显著差异，可能是应用存在间歇性的问题。 （或者，原因可能出在某个设计功能上，如缓存。）
* **探查器跟踪**：一个图标，指示探查器已捕获此操作的堆栈跟踪。

选择“视图”打开跟踪浏览器。 该浏览器显示探查器捕获的多个样本（已按响应时间分类）。

如果正在使用“预览性能”窗格，请转到此页的“执行操作”部分查看探查器跟踪。 选择“探查器跟踪”按钮。

![“Application Insights 性能”窗格预览探查器跟踪][performance-blade-v2-examples]

选择一个样本可以显示执行该请求所花费的时间的代码级细节。

![Application Insights 跟踪浏览器][trace-explorer]

跟踪浏览器会显示以下信息：

* **显示热路径**：打开最大的叶节点，或者至少打开某个接近的节点。 大多数情况下，此节点与性能瓶颈相邻。
* **标签**：函数或事件的名称。 树中显示了代码与所发生事件（如 SQL 和 HTTP 事件）的混合形式。 最前面的事件表示请求总持续时间。
* **所用时间**：从操作开始到操作结束之间的时间间隔。
* **时间**：运行某个函数/事件的时间相对于运行其他函数的时间。

## <a name="how-to-read-performance-data"></a>如何读取性能数据

Microsoft 服务探查器结合使用采样方法和检测来分析应用程序的性能。 当详细收集操作正在进行时，服务探查器将每隔一毫秒对每台计算机的 CPU 指令指针采样。 每个样本捕获当前正在执行的线程的完整调用堆栈。 它从高级抽象化和低级抽象化两个角度，提供有关线程当前行为的有用详细信息。 服务探查器还会收集其他事件（包括上下文切换事件、任务并行库 (TPL) 事件和线程池事件），以跟踪活动相关性和因果关系。

时间线视图中显示的调用堆栈是采样和检测的结果。 由于每个样本会捕获线程的整个调用堆栈，因此包含 Microsoft .NET Framework 中的代码，以及引用的其他框架。

### <a id="jitnewobj"></a>对象分配（clr!JIT\_New 或 clr!JIT\_Newarr1）
clr!JIT\_New 和 clr!JIT\_Newarr1 是 .NET Framework 中的 helper 函数，用于分配托管堆中的内存。 分配对象时，将调用 clr!JIT\_New。 分配对象数组时，将调用 clr!JIT\_Newarr1。 这两个函数通常速度很快，花费的时间相对较短。 如果发现时间线中的 clr!JIT\_New 或 clr!JIT\_Newarr1 花费了很长时间，则可能表示代码分配了很多对象，从而消耗了大量的内存。

### <a id="theprestub"></a>加载代码 (clr!ThePreStub)
clr!ThePreStub 是 .NET Framework 中的 helper 函数，用于准备首次执行的代码。 这通常包括但不限于实时 (JIT) 编译。 对于每个 C# 方法，在进程的生存期内，最多只应调用 clr!ThePreStub 一次。

如果 clr!ThePreStub 针对某个请求花费了很长时间，则表示这是第一个执行该方法的请求。 .NET Framework 运行时加载该方法的时间非常重要。 可以考虑在用户访问该代码部分之前使用执行该代码部分的预热进程，或者考虑在程序集中运行本机映像生成器 (ngen.exe)。

### <a id="lockcontention"></a>锁争用（clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker）
clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker 指示当前线程正在等待释放锁。 执行 C# LOCK 语句、调用 Monitor.Enter 方法或者结合 MethodImplOptions.Synchronized 属性调用某个方法时，通常会显示这种状态。 如果线程 A 获取了某个锁，而线程 B 在线程 A 释放该锁之前尝试获取同一个锁，此时通常会发生锁争用。

### <a id="ngencold"></a>加载代码 ([COLD])
如果方法名称包含 [COLD]（例如 mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined），则表示 .NET Framework 运行时首次执行的代码未经过<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">按配置优化</a>功能的优化。 对于每个方法，在进程的生存期内，这种状态最多只应出现一次。

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
“时间”列是针对节点收集的非独占样本在各个时间发生的变化的可视化效果。 请求的总范围划分成 32 个时间存储桶。 该节点的非独占样本会在这 32 个存储桶中累积。 每个存储桶用一个条形表示。 条形的高度表示缩放后的值。 对于带有 CPU_TIME 或 BLOCKED_TIME 标记的节点，或者资源（CPU、磁盘、线程）的消耗存在某种明显关系时，条形表示在该桶的时间段内消耗了其中的某个资源。 如果消耗多个资源，这些指标的值可能大于 100%。 例如，如果在某个时间间隔内平均使用两个 CPU，则指标值为 200%。


## <a id="troubleshooting"></a>故障排除

### <a name="too-many-active-profiling-sessions"></a>活动分析会话太多

目前，最多可以对同一服务计划上运行的 4 个 Azure Web 应用和部署槽启用探查器。 如果探查器 Web 作业报告活动分析会话太多，请将一些 Web 应用移到不同的服务计划。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何确定 Application Insights Profiler 是否正在运行？

探查器在 Web 应用中以连续 Web 作业的形式运行。 可以在 [Azure 门户](https://portal.azure.com)中打开 Web 应用资源。 在“WebJobs”窗格中，查看 ApplicationInsightsProfiler 的状态。 如果探查器未运行，请打开“日志”获取详细信息。

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>为何即使运行了探查器，也找不到任何堆栈示例？

下面是可以检查的几个因素：

* 确保 Web 应用服务计划属于基本层或更高层。
* 确保 Web 应用中已启用 Application Insights SDK 2.2 Beta 或更高版本。
* 确保配置 Web 应用中的 APPINSIGHTS_INSTRUMENTATIONKEY 设置所采用的检测密钥与 Application Insights SDK 使用的检测密钥相同。
* 确保 Web 应用在 .NET Framework 4.6 上运行。
* 如果 Web 应用是一个 ASP.NET Core 应用程序，请检查[所需的依赖项](#aspnetcore)。

启动探查器后，将有短暂的一段预热时间，在此期间，探查器会活跃地收集多种性能跟踪。 此后，探查器将每隔一小时收集性能跟踪两分钟。  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>使用 Azure 服务事件探查器时出现问题， 原因是什么？  

启用 Application Insights Profiler 时，会禁用 Azure 服务探查器代理。

### <a id="double-counting"></a>并行线程的重复计算

在某些情况下，堆栈查看器中的总时间指标大于请求的持续时间。

如果存在与请求关联的两个或更多个线程并且它们并行运行，则可能会发生这种情况。 在这种情况下，总线程时间就会超过已用时间。 一个线程可能会等待另一个线程完成。 查看器会尝试检测此数据并省略不相关的等待时间，但是，它会倾向于显示过多信息，而不是省略关键信息。  

如果看到跟踪中出现并行线程，请确定哪些线程处于等待状态，以便可以确定请求的关键路径。 在大多数情况下，快速进入等待状态的线程只是在等待其他线程完成。 请专注于其他线程，忽略等待中线程花费的时间。

### <a id="issue-loading-trace-in-viewer"></a>无探查数据

下面是可以检查的几个因素：

* 如果要查看的数据的期限超过了好几周，请尝试限制时间筛选器并重试。
* 检查代理或防火墙是否未阻止对 https://gateway.azureserviceprofiler.net 的访问。
* 检查在应用中使用的 Application Insights 检测密钥是否与启用探查的 Application Insights 资源使用的检测密钥相同。 密钥通常在 ApplicationInsights.config 中，但也可能在 web.config 或 app.config 文件中。

### <a name="error-report-in-the-profiling-viewer"></a>探查查看器中的错误报告

在门户中提交支持票证。 请务必包含错误消息中的相关性 ID。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署错误：目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”

如果在已启用探查器的情况下将 Web 应用重新部署到应用服务资源，可能会看到如下消息：

目录不为空“D:\\home\\site\\wwwroot\\App_Data\\jobs”

如果通过脚本或通过 Visual Studio Team Services 部署管道运行 Web 部署，则会发生此错误。 解决方法是将以下附加部署参数添加到 Web 部署任务：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

这些参数将删除 Application Insights Profiler 所用的文件夹，并取消阻止重新部署进程。 它们不会影响当前运行的探查器实例。


## <a name="manual-installation"></a>手动安装

配置探查器时，将对 Web 应用的设置进行更新。 如果你的环境有此要求，则可以手动应用更新。 例如在适用于 PowerApps 的应用服务环境中运行应用程序的情况。

1. 在 Web 应用控制窗格中，打开“设置”。
2. 将“.Net Framework 版本”设置为“v4.6”。
3. 将“Always On”设置为“打开”。
4. 添加应用设置“APPINSIGHTS_INSTRUMENTATIONKEY”，将值设置为 SDK 使用的同一检测密钥。
5. 打开“高级工具”。
6. 选择“转到”打开 Kudu 网站。
7. 在 Kudu 网站上，选择“站点扩展”。
8. 从 Azure Web 应用库中安装“Application Insights”。
9. 重新启动 Web 应用。

## <a id="profileondemand"></a>手动触发探查器
开发探查器时，我们添加了一个命令行接口，以便可以测试应用服务中的探查器。 用户也可以使用此接口来自定义探查器的启动方式。 在较高层面上，探查器使用应用服务的 Kudu 系统在后台管理探查。 当你安装 Application Insights 扩展时，我们会创建一个用于托管探查器的连续 Web 作业。 我们将使用与此相同的技术创建一个新的 Web 作业，你可以根据需要自定义此作业。

本部分介绍如何：

1.  创建一个 Web 作业。只需按下某个按钮，该作业就能将探查器启动两分钟。
2.  创建一个可以计划运行探查器的 Web 作业。
3.  设置探查器的参数。


### <a name="set-up"></a>设置
首先，让我们熟悉 Web 作业的仪表板。 在“设置”下面，单击“Web 作业”选项卡。

![Web 作业边栏选项卡](./media/app-insights-profiler/webjobs-blade.png)

可以看到，此仪表板显示了站点上当前安装的所有 Web 作业。 可以看到正在运行探查器作业的 ApplicationInsightsProfiler2 Web 作业。 我们将在此位置为手动和计划探查完成创建新的 Web 作业。

首先，让我们获取所需的二进制文件。

1.  转到 kudu 站点。 在“开发工具”选项卡下面，单击带有 Kudu 徽标的“高级工具”选项卡。 单击“转到”。 随后将会转到一个新站点并自动登录。
2.  接下来，需要下载探查器二进制文件。 通过页面顶部的“调试控制台”->“CMD”导航到文件资源管理器。
3.  单击“站点”->“wwwroot”->“App_Data”->“作业”->“连续”。 此时应会看到“ApplicationInsightsProfiler2”文件夹。 单击该文件夹左侧的下载图标。 随后将会下载“ApplicationInsightsProfiler2.zip”文件。
4.  这会下载后续步骤所需的所有文件。 我建议创建一个空目录，以便在继续操作之前，将此 zip 存档移到该目录。

### <a name="setting-up-the-web-job-archive"></a>设置 Web 作业存档
简单而言，将新的 Web 作业添加到 Azure 网站就是创建一个包含 run.cmd 的 zip 存档。 该 run.cmd 告知 Web 作业系统在你运行该 Web 作业时要执行哪些操作。 可以查看 Web 作业文档中的其他选项，但对于本教程，我们不需要其他任何选项。

1.  开始创建新文件夹，并将其命名为“RunProfiler2Minutes”。
2.  将提取的 ApplicationInsightProfiler2 文件夹中的文件复制到此新文件夹。
3.  创建新的 run.cmd 文件。 （为方便起见，在开始之前，我已在 VS Code 中打开此工作文件夹）
4.  添加命令 `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` 并保存文件。
a.  `start` 命令指示探查器启动。
b.  `--engine-mode immediate` 告知探查器，我们要立即开始探查。
c.  `--single` 表示运行，然后自动停止。  `--immediate-profiling-duration 120` 表示让探查器运行 120 秒，即 2 分钟。
5.  保存此文件。
6.  将此文件夹存档：可以右键单击该文件夹并选择“发送到”->“压缩(zip)文件夹”。 这会使用文件夹的名称创建一个 .zip 文件。

![启动探查器命令](./media/app-insights-profiler/start-profiler-command.png)

创建 Web 作业 .zip 文件后，可以在站点中使用它来设置 Web 作业。

### <a name="add-a-new-web-job"></a>添加新的 Web 作业
接下来，在站点中添加一个新的 Web 作业。 本示例将演示如何添加手动触发的 Web 作业。 完成此过程后，计划 Web 作业的过程几乎相同。 你可以自行阅读有关计划触发的作业的详细信息。

1.  转到 Web 作业仪表板。
2.  在工具栏中单击“添加”命令。
3.  为 Web 作业命名，为方便阅读，我使用了与存档相同的名称，同时也方便将它打开以包含不同版本的 run.cmd。
4.  在窗体的文件上传部分中，单击“打开文件”图标并找到前面创建的 .zip 文件。
5.  对于“类型”，请选择“已触发”。
6.  对于“触发器”，请选择“手动”。
7.  点击“确定”以保存。

![启动探查器命令](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>运行探查器

添加可以手动触发的新 Web 作业后，可以尝试运行它。

1.  根据设计，在任意给定时间，计算机上只能运行一个 ApplicationInsightsProfiler.exe 进程。 因此，若要开始运行探查器，请务必在此仪表板中禁用“连续”Web 作业。 单击相应的行并按“停止”。 在工具栏上刷新，并确认状态是否显示作业已停止。
2.  单击添加的新 Web 作业所在的行，并按“运行”。
3.  在仍旧选中该行的情况下，单击工具栏中的“日志”命令，转到已启动的 Web 作业的 Web 作业仪表板。 仪表板中列出了最近的运行及其结果。
4.  单击刚刚启动的运行。
5.  如果一切正常，将会看到探查器发来的一些诊断日志，表示探查已经开始。

### <a name="things-to-consider"></a>注意事项

尽管此方法相对简单直接，但需要注意一些问题。

1.  由于此过程不是由我们的服务进行管理，因此，我们无法更新 Web 作业的代理二进制文件。 目前，二进制文件没有稳定的下载页，获取最新文件的唯一方法是更新扩展，然后从 continuous 文件夹中抓取文件，就像我们前面所做的一样。
2.  由于此过程使用的命令行参数最初设计为面向开发人员而不是最终用户，这些参数将来可能会更改，因此，在升级时请注意这一点。 这不会造成太大的问题，因为可以添加 Web 作业、运行它，然后测试它是否正常工作。 最终我们会生成 UI 来执行此操作而无需手动过程，但这是一个需要考虑的问题。
3.  应用服务的“Web 作业”功能独特之处在于，当它运行 Web 作业时，可以确保进程使用的环境变量和应用设置，与网站最终使用的环境变量和应用设置相同。 这意味着，不需要通过命令行将检测密钥传递给探查器，探查器只会从环境中拾取检测密钥。 但是，如果想要在应用服务外部的开发机箱或计算机上运行探查器，则需要提供检测密钥。 为此，可以传入参数 `--ikey <instrumentation-key>`。 请注意，此值必须与应用程序使用的检测密钥匹配。 探查器的日志输出将会告知探查器是使用哪个 ikey 启动的，以及在探查时，我们是否通过该检测密钥检测到活动。
4.  手动触发的 Web 作业实际上可以通过 Web 挂钩来触发。 可通过以下方式获取此 URL：在仪表板中右键单击 Web 作业并查看属性，或者在从表中选择 Web 作业后，选择工具栏中的“属性”。 有大量在线文章介绍了此方法，因此我不会过多地重述。此方法为通过 CI/CD 管道（例如 VSTS）或 Microsoft Flow (https://flow.microsoft.com/en-us/) 触发探查器带来了可能性。 根据 run.cmd 的复杂程度（例如，创建 run.ps1），可能的方法有多种多样。  




## <a id="aspnetcore"></a>ASP.NET Core 支持

ASP.NET Core 应用程序需要安装 Microsoft.ApplicationInsights.AspNetCore NuGet 包 2.1.0-beta6 或更高版本才能使用探查器。 自 2017 年 6 月 27 日起，以前的版本将不再受支持。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

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
