<properties 
	pageTitle="使用 Application Insights 为 ASP.NET 设置 Web 应用分析 | Microsoft Azure" 
	description="为托管在本地或 Azure 中的 ASP.NET 网站配置性能、可用性和使用情况分析。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="NumberByColors" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/09/2016" 
	ms.author="daviste"/>


# 设置用于 ASP.NET 的 Application Insights

[Visual Studio Application Insights](app-insights-overview.md) 监视活动应用程序，帮助[检测和诊断性能问题及异常](app-insights-detect-triage-diagnose.md)，同时[探索应用的使用情况](app-insights-overview-usage.md)。它适用于在自有本地 IIS 服务器或云 VM 上托管的应用，以及 Azure Web 应用。


## 开始之前

你需要：

* Visual Studio 2013 Update 3 或更高版本。版本越高越好。
* [Microsoft Azure](http://azure.com) 订阅。如果你的团队或组织拥有 Azure 订阅，则所有者可以使用你的 [Microsoft 帐户](http://live.com)将你加入其中。

如果对以下内容感兴趣，请查看其他文章：

* [在运行时检测 Web 应用](app-insights-monitor-performance-live-website-now.md)
* [Azure 云服务](app-insights-cloudservices.md)

## <a name="ide"></a> 1.添加 Application Insights SDK


### 如果是新项目...

在 Visual Studio 中创建新项目时，确保选择 Application Insights。


![创建 ASP.NET 项目](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### …如果是现有项目

右键单击解决方案资源管理器中的项目，然后选择“添加 Application Insights 遥测”或“配置 Application Insights”。

![选择“添加 Application Insights”](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core 项目？- [按照这些说明，修改几行代码](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs)。



## <a name="run"></a> 2.运行您的应用程序

使用 F5 运行应用程序并试用：打开不同的页面来生成一些遥测。

在 Visual Studio 中，将看到已记录事件的计数。

![在 Visual Studio 中，调试期间会显示“Application Insights”按钮。](./media/app-insights-asp-net/54.png)

## 3\.查看遥测...

### ...在 Visual Studio 中

在 Visual Studio 中打开“Application Insights”窗口：单击“Application Insights”按钮，或在解决方案资源管理器中右键单击项目：

![在 Visual Studio 中，调试期间会显示“Application Insights”按钮。](./media/app-insights-asp-net/55.png)

此视图显示在应用的服务器端生成的遥测。使用这些筛选器进行试验，然后单击任何事件以查看更多详细信息。

[了解有关 Visual Studio 中的 Application Insights Tools 的详细信息](app-insights-visual-studio.md)。

<a name="monitor">
### ...在门户中

除非选择“仅安装 SDK”，否则还可以在 Application Insights Web 门户中查看遥测。

该门户中的图表、分析工具和仪表板比 Visual Studio 中的多。


在 [Azure 门户](https://portal.azure.com/)中打开 Application Insights 资源。

![右键单击项目，然后打开 Azure 门户](./media/app-insights-asp-net/appinsights-04-openPortal.png)

门户会打开应用的遥测视图：![](./media/app-insights-asp-net/66.png)

* 第一个遥测会在[实时指标流](app-insights-metrics-explorer.md#live-metrics-stream)中显示。
* 各个事件在“搜索”(1) 中显示。数据可能需要几分钟才会显示。单击任何事件即可查看其属性。
* 聚合指标在图表 (2) 中显示。数据可能需要一两分钟才会在此处显示。单击任何图表即可打开包含更多详细信息的边栏选项卡。

[了解有关在 Azure 门户中使用 Application Insights 的详细信息](app-insights-dashboards.md)。

## 4\.发布应用

将应用发布到 IIS 服务器或 Azure。监视[实时指标流](app-insights-metrics-explorer.md#live-metrics-stream)，确保一切平稳运行。

将在 Application Insights 门户中看到遥测累积，还可在此监视指标、搜索遥测以及设置[仪表板](app-insights-dashboards.md)。还可以使用功能强大的[分析查询语言](app-insights-analytics.md)来分析使用情况和性能，或查找特定事件。

也可以继续在 [Visual Studio](app-insights-visual-studio.md) 中借助诊断搜索和[趋势](app-insights-visual-studio-trends.md)等工具来分析遥测。

> [AZURE.NOTE] 如果应用发送的遥测足够达到[限制](app-insights-pricing.md#limits-summary)，自动[采样](app-insights-sampling.md)会打开。采样可以减少从应用发送的遥测数量，同时为诊断保留相关数据。


##<a name="land"></a>“添加 Application Insights”执行哪些操作？

Application Insights 从应用将遥测发送到 Application Insights 门户（在 Microsoft Azure 中托管）：

![](./media/app-insights-asp-net/01-scheme.png)

因此该命令执行三个操作：

1. 将 Application Insights Web SDK NuGet 包添加到项目。若要在 Visual Studio 中查看，请右键单击项目，然后选择“管理 NuGet 包”。
2. 在 [Azure 门户](https://portal.azure.com/)中创建 Application Insights 资源。这是可以查看数据的位置。它会检索可标识资源的*检测密钥*。
3. 在 `ApplicationInsights.config` 中插入检测密钥，以便 SDK 可以将遥测发送到门户。

如果需要，可以为 [ASP.NET 4](app-insights-windows-services.md) 或 [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) 手动执行这些步骤。

### 升级到 SDK 的未来版本

若要升级到 [SDK 的新版本](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)，请再次打开 NuGet 包管理器，然后筛选已安装的包。选择 Microsoft.ApplicationInsights.Web，然后选择“升级”。

如果对 ApplicationInsights.config 执行了任何自定义操作，请在升级前保存相关副本，并在升级后将更改合并到新版本中。



## 后续步骤

| | 
|---|---
|**[在 Visual Studio 中使用 Application Insights](app-insights-visual-studio.md)**<br/>使用遥测调试、诊断搜索、钻取代码。|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[使用 Application Insights 门户](app-insights-dashboards.md)**<br/>仪表板、功能强大的诊断和分析工具、警报、应用程序的实时依赖项映射和遥测导出。 |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[添加更多数据](app-insights-asp-net-more.md)**<br/>监视使用情况、可用性、依赖项、异常。集成来自记录框架的跟踪。编写自定义遥测。 | ![Visual Studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0921_2016-->