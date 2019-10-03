---
title: 使用 Application Insights Profiler 探查 Azure 中的生产应用程序 | Microsoft Docs
description: 使用一个精简的探查器识别 Web 服务器代码中的热路径。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: debc30a368a0f9ef7be9b0cda0b1238f8e2bc2e3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338074"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>使用 Application Insights 探查 Azure 中的生产应用程序
## <a name="enable-application-insights-profiler-for-your-application"></a>对应用程序启用 Application Insights Profiler

Azure Application Insights Profiler 针对 Azure 生产环境中运行的应用程序提供性能跟踪。 Profiler 可以大规模自动捕获数据，且不会给用户造成负面影响。 Profiler 可帮助识别在处理特定 Web 请求时，花费时间最长的“热”代码路径。 

Profiler 适用于以下 Azure 服务中部署的 .NET 应用程序。 下面提供了有关为每个服务类型启用 Profiler 的具体说明的链接。

* [Azure 应用服务](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**预览** ASP.NET Core Azure Linux Web 应用](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

如果已启用 Profiler 但未看到跟踪，请查看[故障排除指南](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。

## <a name="view-profiler-data"></a>查看 Profiler 数据

要使 Profiler 上传跟踪，应用程序必须主动处理请求。 如果你正在进行试验，可以通过 [Application Insights 性能测试](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)生成针对 Web 应用的请求。 如果最近启用了 Profiler，可以运行简短的负载测试。 运行负载测试时，请选择 [**Profiler 设置页**](profiler-settings.md)中的“立即探查”按钮。 Profiler 开始运行后，它会每小时随机探查大约一次，持续时间为两分钟。 如果应用程序处理的请求流比较稳定，则 Profiler 会每隔一小时上传跟踪。

应用程序收到一些流量后，如果 Profiler 有时间上传跟踪，则你应会获得一些可查看的跟踪。 此过程最多可能需要 5 到 10 分钟。 若要查看跟踪，请在“性能”窗格中选择“采取措施”，然后选择“Profiler 跟踪”按钮。

![“Application Insights 性能”窗格预览 Profiler 跟踪][performance-blade]

选择一个样本可以显示执行该请求所花费时间的代码级细节。

![Application Insights 跟踪浏览器][trace-explorer]

跟踪浏览器会显示以下信息：

* **显示热路径**：打开最大的叶节点，或者至少打开某个接近的节点。 大多数情况下，此节点与性能瓶颈相邻。
* **标签**：函数或事件的名称。 树中显示了代码与发生的事件（例如 SQL 和 HTTP 事件）的混合形式。 最前面的事件表示请求总持续时间。
* **所用时间**：从操作开始到操作结束之间的时间间隔。
* **时间**：运行函数或事件的时间，此值相对于运行其他函数的时间。

## <a name="how-to-read-performance-data"></a>如何读取性能数据

Microsoft 服务探查器结合使用采样方法和检测来分析应用程序的性能。 当详细收集操作正在进行时，服务探查器将每隔一毫秒对每台计算机的 CPU 指令指针采样。 每个样本捕获当前正在执行的线程的完整调用堆栈。 它从高级抽象化和低级抽象化两个角度，提供有关线程当前行为的有用详细信息。 服务探查器还会收集其他事件（包括上下文切换事件、任务并行库 (TPL) 事件和线程池事件），以跟踪活动相关性和因果关系。

时间线视图中显示的调用堆栈是采样和检测的结果。 由于每个样本会捕获线程的整个调用堆栈，因此包含 Microsoft .NET Framework 中的代码，以及引用的其他框架中的代码。

### <a id="jitnewobj"></a>对象分配（clr!JIT\_New 或 clr!JIT\_Newarr1）

**clr!JIT\_New** 和 **clr!JIT\_Newarr1** 是 .NET Framework 中的 helper 函数，用于分配托管堆中的内存。 分配对象时，将调用 clr!JIT\_New。 分配对象数组时，将调用 clr!JIT\_Newarr1。 这两个函数通常速度很快，花费的时间相对较短。 如果时间线中的 **clr!JIT\_New** 或 **clr!JIT\_Newarr1** 花费了很长时间，则可能表示代码分配了很多对象，从而消耗了大量的内存。

### <a id="theprestub"></a>加载代码 (clr!ThePreStub)

**clr!ThePreStub** 是 .NET Framework 中的 helper 函数，用于准备首次执行的代码。 此执行通常包括但不限于实时 (JIT) 编译。 对于每个 C# 方法，在进程的生存期内，最多只应调用 **clr!ThePreStub** 一次。

如果 **clr!ThePreStub** 针对某个请求花费了很长时间，则表示这是第一个执行该方法的请求。 .NET Framework 运行时加载该方法的时间非常重要。 可以考虑在用户访问该代码部分之前使用执行该代码部分的预热进程，或者考虑在程序集中运行本机映像生成器 (ngen.exe)。

### <a id="lockcontention"></a>锁争用（clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker）

clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker 指示当前线程正在等待释放锁。 执行 C# **LOCK** 语句、调用 **Monitor.Enter** 方法或者结合 **MethodImplOptions.Synchronized** 属性调用某个方法时，通常会显示此文本。 如果线程 _A_ 获取了某个锁，而线程 _B_ 在线程 _A_ 释放该锁之前尝试获取同一个锁，此时通常会发生锁争用。

### <a id="ngencold"></a>加载代码 ([COLD])

如果方法名称包含 **[COLD]** （例如 **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**），则表示 .NET Framework 运行时首次执行的代码未经过[按配置优化](/cpp/build/profile-guided-optimizations)功能的优化。 对于每个方法，在进程的生存期内，它最多只应显示一次。

如果针对某个请求加载代码花费的时间很长，则表示这是第一个执行该方法的未优化部分的请求。 请考虑在用户访问该代码部分之前使用执行该代码部分的预热进程。

### <a id="httpclientsend"></a>发送 HTTP 请求

**HttpClient.Send** 等方法指示代码正在等待某个 HTTP 请求完成。

### <a id="sqlcommand"></a>数据库操作

**SqlCommand.Execute** 等方法指示代码正在等待某个数据库操作完成。

### <a id="await"></a>等待 (AWAIT\_TIME)

AWAIT\_TIME 指示代码正在等待另一个任务完成。 这种延迟通常发生在 C# **AWAIT** 语句上。 当代码执行 C# **AWAIT** 时，线程会回退并将控制权返回给线程池，此时，不会有任何阻塞的线程等待 **AWAIT** 完成。 但是，从逻辑上讲，执行 **AWAIT** 的线程会被“阻塞”并等待该操作完成。 AWAIT\_TIME 语句指示等待任务完成的阻塞时间。

### <a id="block"></a>阻塞时间

BLOCKED_TIME 指示代码正在等待另一个资源变为可用。 例如，它可能会等待同步对象或线程变为可用，或等待请求完成。

### <a name="unmanaged-async"></a>非托管异步

.NET Framework 发出 ETW 事件并在线程之间传递活动 ID，以便可以跨线程跟踪异步调用。 非托管代码（本机代码）和一些较旧样式的异步代码缺少这些事件和活动 ID，因此探查器无法分辨线程上运行的线程和函数。 这在调用堆栈中标记为“非托管异步”。 如果下载 ETW 文件，则可以使用 [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) 更深入地了解正在发生的情况。

### <a id="cpu"></a>CPU 时间

CPU 正忙于执行指令。

### <a id="disk"></a>磁盘时间

应用程序正在执行磁盘操作。

### <a id="network"></a>网络时间

应用程序正在执行网络操作。

### <a id="when"></a>“时间”列

“时间”列是针对节点收集的非独占样本在各个时间发生的变化的可视化效果。 请求的总范围划分成 32 个时间存储桶。 该节点的非独占样本会在这 32 个存储桶中累积。 每个存储桶用一个条形表示。 条形的高度表示缩放后的值。 如果节点带有 **CPU_TIME** 或 **BLOCKED_TIME** 标记，或者跟资源（例如，CPU、磁盘、线程）的消耗存在某种明显关系，则条形表示在该 Bucket 的时间段内消耗了其中的某个资源。 如果消耗多个资源，这些指标的值可能大于 100%。 例如，如果在某个时间间隔内平均使用两个 CPU，则指标值为 200%。

## <a name="limitations"></a>限制

默认数据保留期为 5 天。 每天引入的最大数据量为 10 GB。

使用 Profiler 服务没有任何费用。 要使用该服务，Web 应用必须至少托管在 Azure 应用服务 Web 应用功能的基本层中。

## <a name="overhead-and-sampling-algorithm"></a>开销和采样算法

在托管的应用程序已启用 Profiler 来捕获跟踪的每个虚拟机上，Profiler 每小时随机运行 2 分钟。 Profiler 在运行时会给服务器增加 5% 到 15% 的 CPU 开销。

## <a name="next-steps"></a>后续步骤
为 Azure 应用程序启用 Application Insights Profiler。 另请参阅：
* [应用服务](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
