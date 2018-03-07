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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c65ef9141898369b8fcadd4c52972b767aca7cfe
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure Web 应用

Azure Application Insights 的此功能已分别面向 Azure 应用服务的 Web 应用功能和 Azure 计算资源提供正式版和预览版。

本文介绍了使用 [Application Insights](app-insights-overview.md) 时在实时 Web 应用程序的每个方法上花费的时间。 Application Insights Profiler 工具显示了由应用处理的实时请求的详细配置文件。 Profiler 突出显示使用时间最多的热路径。 将会根据采样分析响应时间各不相同的请求。 可以通过各种技术尽量降低与应用程序相关联的开销。

Profiler 目前适用于 Web 应用上运行的 ASP.NET 和 ASP.NET Core Web 应用。 若要使用 Profiler，“基本”服务层或更高层是必需的。

## <a id="installation"></a> 为 Web Apps Web 应用启用 Profiler
如果已将应用程序发布到 Web 应用，但尚未在源代码中执行使用 Application Insights 所需的任何操作，请执行以下操作：
1. 转到 Azure 门户中的“应用服务”窗格。
2. 在“监视”下选择“Application Insights”，然后按窗格中的说明创建新资源，或者选择现有的 Application Insights 资源，以便监视 Web 应用。

   ![在应用服务门户上启用 App Insights][appinsights-in-appservices]

3. 如果可以访问项目源代码，请[安装 Application Insights](app-insights-asp-net.md)。  
   如果已安装，请确保使用的是最新版本。 要检查最新版本，请在解决方案资源管理器中右键单击你的项目，然后选择“管理 NuGet 包” > “更新” > “更新所有包”。 然后部署应用。

ASP.NET Core 应用程序需要安装 Microsoft.ApplicationInsights.AspNetCore NuGet 包 2.1.0-beta6 或更高版本才能使用 Profiler。 从 2017 年 6 月 27 日开始，不支持更早的版本。

1. 在 [Azure 门户](https://portal.azure.com)中，打开 Web 应用的 Application Insights 资源。 
2. 选择“性能” > “启用 Application Insights Profiler”。

   ![选择“启用探查器”横幅][enable-profiler-banner]

3. 另外，可以通过选择“Profiler”配置来查看状态，启用或禁用 Profiler。

   ![ASP.NET Profiler 配置][performance-blade]

   配置有 Application Insights 的 Web 应用会在“探查器”配置窗格中列出。 如果遵循了上述步骤，则应该已安装 Profiler 代理。 

4. 在“Profiler”配置窗格中选择“启用 Profiler”。

5. 如果需要，请根据说明安装 Profiler 代理。 如果尚无 Web 应用配置有 Application Insights，请选择“添加链接应用”。

   ![“配置”窗格选项][linked app services]

与通过 Web 应用计划托管的 Web 应用不同，在 Azure 计算资源（例如：Azure 虚拟机、虚拟机规模集、Azure Service Fabric 或 Azure 云服务）中托管的应用程序不由 Azure 直接管理。 在这种情况下，将没有任何要链接到的 Web 应用。 请改为选择“启用探查器”按钮，而不是链接到应用。

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>为 Azure 计算资源启用 Profiler（预览）

有关信息，请参阅[用于 Azure 计算资源的 Profiler 的预览版本](https://go.microsoft.com/fwlink/?linkid=848155)。

## <a name="view-profiler-data"></a>查看探查器数据

确保应用程序可以接收流量。 如果正在进行试验，可以通过 [Application Insights 性能测试](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test)生成针对 Web 应用的请求。 如果最近启用了 Profiler，则可以运行简短的负载测试大约 15 分钟，这样就会生成探查器跟踪。 如果已启用 Profiler 相当长一段时间，请记住，Profiler 每小时会随机运行两次，每次运行两分钟。 建议运行负载测试一个小时，确保获取探查器跟踪样本。

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
* 如果 Web 应用是一个 ASP.NET Core 应用程序，请检查[所需的依赖项](#aspnetcore)。

启动 Profiler 后，将有短暂的一段预热时间，在此期间，Profiler 会积极收集多种性能跟踪。 此后，Profiler 会每隔一小时收集性能跟踪两分钟。

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
* 确保在应用中使用的 Application Insights 检测密钥与启用探查所用的 Application Insights 资源使用的检测密钥相同。 密钥通常在 ApplicationInsights.config 文件中，但也可能在 web.config 或 app.config 文件中。

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
2. 将“.Net Framework 版本”设置为“v4.6”。
3. 将“Always On”设置为“打开”。
4. 添加应用设置“APPINSIGHTS_INSTRUMENTATIONKEY”，将值设置为 SDK 使用的同一检测密钥。
5. 打开“高级工具”。
6. 选择“转到”打开 Kudu 网站。
7. 在 Kudu 网站上，选择“站点扩展”。
8. 从 Azure Web 应用库中安装“Application Insights”。
9. 重新启动 Web 应用。

## <a id="profileondemand"></a> 手动触发 Profiler
开发 Profiler 时，我们添加了一个命令行界面，以便测试应用服务中的 Profiler。 用户也可以使用此界面来自定义 Profiler 的启动方式。 在较高层面上，Profiler 使用 Web 应用 Kudu 系统在后台管理探查。 当你安装 Application Insights 扩展时，我们会创建一个用于托管 Profiler 的连续 Web 作业。 我们将使用与此相同的技术创建一个新的 Web 作业，你可以根据需要自定义此作业。

本部分介绍如何：

* 创建一个 Web 作业。只需按下某个按钮，该作业就能将 Profiler 启动两分钟。
* 创建一个可以计划运行 Profiler 的 Web 作业。
* 设置 Profiler 的参数。


### <a name="set-up"></a>设置
首先，请自行熟悉 Web 作业的仪表板。 在“设置”下选择“WebJobs”选项卡。

![Web 作业边栏选项卡](./media/app-insights-profiler/webjobs-blade.png)

可以看到，此仪表板显示了站点上当前安装的所有 Web 作业。 可以看到正在运行 Profiler 作业的 ApplicationInsightsProfiler2 Web 作业。 我们将在此位置为手动和计划探查创建新的 Web 作业。

若要获取所需的二进制文件，请执行以下操作：

1.  在 Kudu 站点的“开发工具”选项卡下面，选择带有 Kudu 徽标的“高级工具”选项卡，然后选择“转到”。  
   此时会打开新站点，你会自动登录。
2.  若要下载 Profiler 二进制文件，请通过“调试控制台” > “CMD”转到位于页面顶部的文件资源管理器。
3.  选择“站点” > “wwwroot” > “App_Data” > “作业” > “连续”。  
   此时会看到“ApplicationInsightsProfiler2”文件夹。 
4. 在文件夹左侧选择“下载”图标。  
   此操作下载 *ApplicationInsightsProfiler2.zip* 文件。 建议创建一个空目录，以便将此 zip 存档移到该目录。

### <a name="setting-up-the-web-job-archive"></a>设置 Web 作业存档
实质上，将新的 Web 作业添加到 Azure 网站就是创建一个包含 *run.cmd* 文件的 zip 存档。 该 *run.cmd* 文件告知 Web 作业系统在你运行该 Web 作业时要执行哪些操作。

1.  创建新文件夹（例如，*RunProfiler2Minutes*）。
2.  将提取的 *ApplicationInsightProfiler2* 文件夹中的文件复制到此新文件夹。
3.  创建新的 *run.cmd* 文件。  
    为方便起见，可以在启动之前打开 Visual Studio Code 中的工作文件夹。
4.  在文件中添加命令 `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`。 这些命令如下所述：

    * `start`：告知 Profiler 启动。  
    * `--engine-mode immediate`：告知 Profiler 立即开始探查。  
    * `--single`：告知 Profiler 自动地先运行后停止。  
    * `--immediate-profiling-duration 120`：告知 Profiler 运行 120 秒，即 2 分钟。

5.  保存所做更改。
6.  将文件夹存档，方法是：右键单击该文件夹，然后选择“发送到” > “压缩的(zip 格式)文件夹”。  
   此操作使用文件夹的名称创建一个 .zip 文件。

![启动 Profiler 命令](./media/app-insights-profiler/start-profiler-command.png)

现在已创建 Web 作业 .zip 文件，该文件可以用来在站点中设置 Web 作业。

### <a name="add-a-new-web-job"></a>添加新的 Web 作业
此部分在站点中添加新的 Web 作业。 以下示例演示如何添加手动触发的 Web 作业。 添加手动触发的 Web 作业以后，过程与计划内 Web 作业几乎相同。

1.  转到“Web 作业”仪表板。
2.  在工具栏中选择“添加”。
3.  为 Web 作业提供一个名称。  
    具体说来，它可以用来匹配存档的名称并将其打开，不管 *run.cmd* 文件有多少不同的版本。
4.  在窗体的“文件上传”区域，选择“打开文件”图标，然后搜索在上一部分创建的 .zip 文件。

    a.  在“类型”框中，选择“已触发”。  
    b.  在“触发器”框中，选择“手动”。

5.  选择“确定”。

![启动 Profiler 命令](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>运行 Profiler

有了可以手动触发的新 Web 作业以后，即可尝试按此部分的说明运行它。

根据设计，在任意给定时间，计算机上只能运行一个 *ApplicationInsightsProfiler.exe* 进程。 因此，在开始之前，请务必在此仪表板中禁用“连续”Web 作业。 
1. 选择新 Web 作业所在的行，然后选择“停止”。 
2. 在工具栏上选择“刷新”，并确认状态是否显示作业已停止。
3. 选择新 Web 作业所在的行，然后选择“运行”。
4. 在行仍处于选中状态的情况下，在工具栏中选择“日志”命令。  
    此操作为新的 Web 作业打开 Web 作业仪表板，该仪表板会列出最新的运行及其结果。
5. 选择刚刚启动的运行实例。  
    如果已成功触发新的 Web 作业，可以查看一些来自 Profiler 的诊断日志，这些日志确认探查已启动。

### <a name="things-to-consider"></a>注意事项

尽管此方法相对简单直接，但需要注意以下问题：

* 由于 Web 作业不是由我们的服务进行管理，因此，我们无法更新 Web 作业的代理二进制文件。 目前，二进制文件没有稳定的下载页，因此获取最新二进制文件的唯一方法是更新扩展，然后从 *Continuous* 文件夹中抓取文件，就像前面所做的一样。

* 由于此过程使用的命令行参数本来是为开发人员而不是最终用户设计的，因此这些参数未来可能会变化。 升级时，请注意可能会有的变化。 这不会造成太大的问题，因为可以添加 Web 作业，运行它，然后进行测试，确保它正常工作。 最终我们会生成一个 UI 来处理这种情况，不使用手动过程。

* Web 应用的 Web 作业功能是独特的。 当它运行 Web 作业时，可以确保进程使用的环境变量和应用设置，与网站将使用的环境变量和应用设置相同。 这意味着，不需要通过命令行将检测密钥传递给 Profiler。 Profiler 会从环境中拾取检测密钥。 但是，如果想要在 Web 应用外部的开发机箱或计算机上运行 Profiler，则需要提供检测密钥。 为此，可以传入参数 `--ikey <instrumentation-key>`。 请注意，此值必须与应用程序正使用的检测密钥匹配。 Profiler 的日志输出会告知你 Profiler 是使用哪个 ikey 启动的，以及在探查时，我们是否通过该检测密钥检测到活动。

* 手动触发的 Web 作业可以通过 Web 挂钩来触发。 可以右键单击仪表板上的 Web 作业，然后查看属性来获取此 URL。 也可以先在表中选择 Web 作业，然后在工具栏中选择“属性”。 此方法会开启无限的可能，例如从 CI/CD 管道（例如 VSTS）或 Microsoft Flow (https://flow.microsoft.com/en-us/) 之类的应用程序触发 Profiler。 从根本上讲，具体选择取决于你想要使 *run.cmd* 文件（也可以是 *run.ps1* 文件）达到多复杂的程度，但不管怎样，你可以很灵活地进行操作。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
