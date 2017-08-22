---
title: "用于 Windows 服务器和辅助角色的 Azure Application Insights | Microsoft Docs"
description: "手动将 Application Insights SDK 添加到 ASP.NET 应用程序，以分析使用情况、可用性和性能。"
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: e7b78907fafcee99c807bfe8f7b311986ba7ffcc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/17/2017

---
# <a name="manually-configure-application-insights-for-net-applications"></a>为 .NET 应用程序手动配置 Application Insights

可以对 [Application Insights](app-insights-overview.md) 进行配置，以便监视各种应用程序或应用程序角色、组件或微服务。 对于 Web 应用和服务，Visual Studio 提供[单步配置](app-insights-asp-net.md)。 对于其他类型的 .NET 应用程序，例如后端服务器角色或桌面应用程序，可以手动配置 Application Insights。

![性能监视图表示例](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>开始之前

需要：

* [Microsoft Azure](http://azure.com)订阅。 如果团队或组织拥有 Azure 订阅，则所有者可以使用 [Microsoft 帐户](http://live.com)你将加入其中。
* Visual Studio 2013 或更高版本。

## <a name="add"></a>1.选择 Application Insights 资源

“资源”是指收集数据并在 Azure 门户中显示的地方。 需要决定，是创建新资源，还是共享现有资源。

### <a name="part-of-a-larger-app-use-existing-resource"></a>更大型应用的一部分：使用现有资源

如果 Web 应用程序有多个组件（例如，一个前端 Web 应用，以及一个或多个后端服务），则应将遥测从所有组件发送至同一资源。 这样可以让它们显示在单个“应用程序映射”中，从而跟踪从一个组件到另一个组件的请求。

因此，如果已监视该应用的其他组件，则请直接使用同一资源。

在 [Azure 门户](https://portal.azure.com/)中打开资源。 

### <a name="self-contained-app-create-a-new-resource"></a>自包含应用：创建新资源

如果新应用与其他应用程序无关，则应有其自己的资源。

登录 [Azure 门户](https://portal.azure.com/)，创建新的 Application Insights 资源。 选择 ASP.NET 作为应用程序类型。

![依次单击“新建”、“Application Insights”](./media/app-insights-windows-services/01-new-asp.png)

选择应用程序类型会设置资源边栏选项卡的默认内容。

## <a name="2-copy-the-instrumentation-key"></a>2.复制检测密钥
该密钥用于标识资源。 很快会在 SDK 中安装它，以便将数据定向到资源。

![单击“属性”，选择密钥，并按 Ctrl+C](./media/app-insights-windows-services/02-props-asp.png)

## <a name="sdk"></a>3.在应用程序中安装 Application Insights 程序包
Application Insights 程序包的安装和配置因使用的平台而异。 

1. 在 Visual Studio 中右键单击项目，并选择“管理 NuGet 包”。
   
    ![右键单击项目，并选择“管理 NuGet 包”](./media/app-insights-windows-services/03-nuget.png)
2. 安装适用于 Windows Server 应用的 Application Insights 包“Microsoft.ApplicationInsights.WindowsServer”。
   
    ![搜索“Application Insights”](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *哪个版本？*

    若要尝试最新功能，请选中“包括预发行版”。 相关文档或博客会注明你是否需要预发行版。
    
    *是否可以使用其他包？*
   
    是的。 如果只想使用 API 发送自己的遥测，请选择“Microsoft.ApplicationInsights”。 Windows Server 包会包括 API 以及若干其他包，例如性能计数器集合和依赖项监视。 

### <a name="to-upgrade-to-future-package-versions"></a>升级到未来的程序包版本
我们会不时发布 SDK 的新版本。

要升级到[程序包的新版本](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/)，请再次打开 NuGet 包管理器，并筛选已安装的程序包。 选择“Microsoft.ApplicationInsights.WindowsServer”，并选择“升级”。

如果对 ApplicationInsights.config 执行了任何自定义操作，请在升级前保存相关副本，并在升级后将更改合并到新版本中。

## <a name="4-send-telemetry"></a>4.发送遥测
**如果只安装了 API 包：**

* 在代码中设置检测密钥，例如，在 `main()`中： 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *你的密钥* `";` 
* [使用 API 编写自己的遥测](app-insights-api-custom-events-metrics.md#ikey)。

**如果安装了其他 Application Insights 包，** 可以视需要使用 .config 文件来设置检测密钥：

* 编辑 ApplicationInsights.config（通过 NuGet 安装添加）。 在结束标记前插入此内容：
  
    `<InstrumentationKey>` *复制的检测密钥* `</InstrumentationKey>`
* 确保解决方案资源管理器中的 ApplicationInsights.config 的属性设置为“生成操作”=“内容”、“复制到输出目录”=“复制” 。

如果要[针对不同生成配置切换密钥](app-insights-separate-resources.md)，则在代码中设置检测密钥会很有用。 如果在代码中设置密钥，则无需在 `.config` 文件中设置它。

## <a name="run"></a> 运行项目
使用 **F5** 运行应用程序并试用：打开不同的页面来生成一些遥测。

在 Visual Studio 中，将看到已发送事件的计数。

![Visual Studio 中的事件计数](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> 查看遥测
返回 [Azure 门户](https://portal.azure.com/) ，浏览到 Application Insights 资源。

在“概述”图表中查找数据。 首先，只会看到一个或两个点。 例如：

![单击浏览更多数据](./media/app-insights-windows-services/12-first-perf.png)

单击任何图表以查看更详细的指标。 [了解有关指标的详细信息。](app-insights-web-monitor-performance.md)

### <a name="no-data"></a>没有数据？
* 使用应用程序打开不同的页面，以生成一些遥测。
* 打开 [“搜索”](app-insights-diagnostic-search.md) 磁贴，查看每个事件。 有时，事件会需要较长的时间才能通过指标管道。
* 请等待几秒钟，并单击“刷新” 。 图表会定期刷新本身，但如果正在等待某些数据显示，则可以手动刷新。
* 请参阅 [疑难解答](app-insights-troubleshoot-faq.md)。

## <a name="publish-your-app"></a>发布应用
现在，将应用程序部署到服务器或 Azure，然后观看数据累积。

![使用 Visual Studio 发布应用](./media/app-insights-windows-services/15-publish.png)

在调试模式下运行时，系统通过管道加速遥测，因此应该可以在数秒内看到数据。 以发布配置部署应用时，数据的累积速度会更慢。

### <a name="no-data-after-you-publish-to-your-server"></a>发布到服务器后却没有数据？
在服务器的防火墙中打开出口流量的端口。 若要获取所需地址的列表，请参阅[此页](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) 

### <a name="trouble-on-your-build-server"></a>生成服务器遇到问题？
请参阅 [此疑难解答项](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)。

> [!NOTE]
> 如果应用生成大量遥测，自适应采样模块将通过仅发送具有代表性的事件部分自动减少发送到门户的量。 但是，以组为单位选择或取消选择与同一请求相关的事件，以便可以在相关事件之间浏览。 
> [了解采样](app-insights-sampling.md)。
> 
> 

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤
* [添加更多遥测](app-insights-asp-net-more.md) ，获取应用程序的 360 度完整视图。


