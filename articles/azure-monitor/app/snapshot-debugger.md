---
title: 适用于 .NET 应用的 Azure 应用程序 Insights Snapshot Debugger
description: 生产 .NET 应用中出现异常时会自动收集调试快照
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 44b4a7bb5910f7f2d89a9f76e21ccfcacda667fb
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932573"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 应用中发生异常时的调试快照
发生异常时，可自动从实时 Web 应用程序收集调试快照。 快照显示发生异常时源代码和变量的状态。 [Azure 应用程序 Insights](../../azure-monitor/app/app-insights-overview.md)中的 Snapshot Debugger 会监视来自你的 web 应用的异常遥测。 它可收集常出现的异常的调试快照，为诊断生产中的问题提供所需信息。 在应用程序中包括[快照收集器 NuGet 包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，还可以选择在[applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中配置集合参数。快照在 Application Insights 门户中出现[异常](../../azure-monitor/app/asp-net-exceptions.md)。

可在门户中查看调试快照，查看调用堆栈并检查每个调用堆栈帧中的变量。 若要使用源代码获取更强大的调试体验，请使用 Visual Studio 2019 企业版打开快照。 在 Visual Studio 中，还可以[将快照设置为以交互方式拍摄快照](https://aka.ms/snappoint)，而无需等待异常。

调试快照存储15天。 此保留策略是逐个应用程序进行设置。 如果需要，可以在 Azure 门户中打开支持案例，以请求增加此值。

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>为应用程序启用 Application Insights Snapshot Debugger
快照集合可用于：
* 运行 .NET Framework 4.5 或更高版本的 .NET Framework 和 ASP.NET 应用程序。
* 在 Windows 上运行的 .NET Core 2.0 和 ASP.NET Core 2.0 应用程序。

支持以下环境：

* [Azure 应用服务](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* 运行 OS 系列4或更高版本的[Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* 在 Windows Server 2012 R2 或更高版本上运行的[Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* 运行 Windows Server 2012 R2 或更高版本的[Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)运行 Windows Server 2012 R2 或更高版本或更高版本 Windows 8.1 或更高版本的物理计算机

> [!NOTE]
> 不支持客户端应用程序（例如，WPF、Windows 窗体或 UWP）。

如果已启用 Snapshot Debugger 但未查看快照，请查看[故障排除指南](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

## <a name="grant-permissions"></a>授予权限

对快照的访问受基于角色的访问控制 (RBAC) 的保护。 若要检查快照，必须先由订阅所有者将你添加到必需的角色。

> [!NOTE]
> 所有者和参与者不会自动具有此角色。 如果他们想要查看快照，他们必须将自己添加到此角色。

订阅所有者应将 `Application Insights Snapshot Debugger` 角色分配给要检查快照的用户。 订阅所有者可将该角色分配给目标 Application Insights 资源或其资源组或订阅的各个用户或组。

1. 在 Azure 门户中导航到 Application Insights 资源。
1. 单击“访问控制(IAM)”。
1. 单击“+添加角色分配”按钮。
1. 从“角色”下拉列表中，选择“Application Insights 快照调试程序”。
1. 搜索并输入要添加的用户的名称。
1. 单击“保存”按钮将用户添加到角色。


> [!IMPORTANT]
> 快照的变量和参数值中可能包含个人信息和其他敏感信息。

## <a name="view-snapshots-in-the-portal"></a>在门户中查看快照

在应用程序中发生异常并且已创建快照后，你应该具有要查看的快照。 可能需要5到10分钟的时间，因为快照已准备就绪，可以从门户查看。 若要查看快照，请在**查看 "** **操作**" 选项卡时选择 "**操作**" 按钮，或在查看 "**异常**" 选项卡时选择 "**例外**" 按钮：

![故障页](./media/snapshot-debugger/failures-page.png)

在右窗格中选择 "操作" 或 "例外"，打开 "**端到端事务详细信息**" 窗格，然后选择 "异常" 事件。 如果某个快照适用于给定的异常，则会在右侧窗格中显示 "**打开调试快照**" 按钮，其中包含[异常](../../azure-monitor/app/asp-net-exceptions.md)的详细信息。

![异常区域中的“打开调试快照”按钮](./media/snapshot-debugger/e2e-transaction-page.png)

在“调试快照”视图中，可以看到调用堆栈和变量窗格。 当在调用堆栈窗格中选择调用堆栈帧时，可在变量窗格中查看该函数调用的局部变量和参数。

![在门户中查看调试快照](./media/snapshot-debugger/open-snapshot-portal.png)

快照可能包含敏感信息，默认情况下不可查看。 要查看快照，必须获取 `Application Insights Snapshot Debugger` 角色。

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>在 Visual Studio 2017 Enterprise 或更高版本中查看快照
1. 单击 "**下载快照**" 按钮，下载可由 Visual Studio Enterprise 打开的 `.diagsession` 文件。

2. 若要打开 `.diagsession` 文件，需要安装 Snapshot Debugger Visual Studio 组件。 Snapshot Debugger 组件是 Visual Studio 中 ASP.net 工作负荷的必需组件，可从 Visual Studio 安装程序的单个组件列表中进行选择。 如果使用 Visual studio 2017 版本15.5 之前的 Visual Studio 版本，则需要从[Visual Studio Marketplace](https://aka.ms/snapshotdebugger)安装该扩展。

3. 打开快照文件后，将出现 Visual Studio 中的“小型转储调试”页面。 单击“调试托管代码”开始调试快照。 快照将打开到引发异常的代码行，以便可以调试进程的当前状态。

    ![在 Visual Studio 中查看调试快照](./media/snapshot-debugger/open-snapshot-visualstudio.png)

下载的快照中包含在 Web 应用程序服务器上找到的所有符号文件。 需要使用这些符号文件将快照数据与源代码相关联。 对于应用服务应用，请确保在发布 Web 应用时启用符号部署。

## <a name="how-snapshots-work"></a>快照的工作原理

快照收集器以 [Application Insights 遥测处理器](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)的形式实现。 应用程序运行时，会将快照收集器遥测处理器添加到应用程序的遥测管道中。
每次应用程序调用 [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions) 时，快照收集器都会根据引发的异常类型和引发方法计算问题 ID。
每次应用程序调用 TrackException 时，计数器都会递增以获得相应的问题 ID。 当计数器达到 `ThresholdForSnapshotting` 值时，问题 ID 将添加到收集计划。

快照收集器还通过订阅 [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) 事件来监视引发的异常。 当该事件触发时，将计算异常的问题 ID 并将其与收集计划中的问题 ID 进行比较。
如果匹配，则将创建正在运行的进程的快照。 将为快照分配一个唯一标识符，并使用该标识符标记异常。 FirstChanceException 处理程序返回后，将按正常方式处理引发的异常。 最终，异常会再次到达 TrackException 方法，该方法将异常与快照标识符一起报告给 Application Insights。

主进程会继续运行并向用户提供流量，几乎没有中断。 同时，快照将传递给快照上传程序进程。 快照上传程序会创建一个小型转储并将其连同任何相关的符号 (.pdb) 文件一起上传到 Application Insights。

> [!TIP]
> - 进程快照是正在运行的进程的暂停克隆。
> - 创建快照大约需要 10 到 20 毫秒。
> - `ThresholdForSnapshotting` 的默认值为 1。 这也是最小值。 因此，在创建快照之前，应用程序必须触发相同的异常**两次**。
> - 如果想要在 Visual Studio 中调试时生成快照，请将 `IsEnabledInDeveloperMode` 设置为 true。
> - 快照创建速率受 `SnapshotsPerTenMinutesLimit` 设置的限制。 默认情况下，限制为每十分钟一个快照。
> - 每天不超过 50 张的快照可以上传。

## <a name="limitations"></a>限制

默认数据保留期为15天。 对于每个 Application Insights 实例，每天允许的最大快照数为50。

### <a name="publish-symbols"></a>发布符号
快照调试程序要求符号文件位于生产服务器上，这样它才能在 Visual Studio 中解码变量并提供调试体验。
默认情况下，在发布到应用服务时，Visual Studio 2017 版本 15.2（或更高版本）会发布适用于发行版本的符号。 在以前的版本中，需要将以下代码行添加到发布配置文件 `.pubxml`，以便在发布模式下发布符号：

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

对于 Azure 计算和其他类型，请确保符号文件位于主应用程序 .dll 所在的同一文件夹（通常为 `wwwroot/bin`）中，或者可通过当前路径访问。

> [!NOTE]
> 有关可用的不同符号选项的详细信息，请参阅[Visual Studio 文档](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)。 为了获得最佳结果，建议使用 "完整"、"可移植" 或 "嵌入"。

### <a name="optimized-builds"></a>优化的内部版本
在某些情况下，由于 JIT 编译器应用了优化，无法在发布版本中查看局部变量。
但是，在 Azure 应用服务中，快照收集器可以对属于其收集计划的引发方法取消优化。

> [!TIP]
> 在应用服务中安装 Application Insights 站点扩展可获得取消优化支持。

## <a name="next-steps"></a>后续步骤
为应用程序启用 Application Insights Snapshot Debugger：

* [Azure 应用服务](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

除了 Application Insights Snapshot Debugger：
 
* [在代码中设置捕捉点](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications)，无需等待出现异常即可获取快照。
* [诊断 Web 应用中的异常](../../azure-monitor/app/asp-net-exceptions.md)介绍了如何在 Application Insights 中显示更多的异常。
* [智能检测](../../azure-monitor/app/proactive-diagnostics.md)可自动发现性能异常。
