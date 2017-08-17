---
title: "使用 Azure Application Insights 为 ASP.NET 设置 Web 应用分析 | Microsoft 文档"
description: "为托管在本地或 Azure 中的 ASP.NET 网站配置性能、可用性和使用情况分析。"
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 1960fa0e17ea9aa0ca15bc3ecb8ce5b1f6630a60
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>为 ASP.NET 网站设置 Application Insights

此过程将 ASP.NET Web 应用配置为将遥测发送到 [Azure Application Insights](app-insights-overview.md) 服务。 它适用于 ASP.NET 应用，后者托管在自己的 IIS 服务器中或云中。 你会获得图表和功能强大的查询语言，这有助于你了解应用的性能以及用户使用它的方式。另外，在出现故障或性能问题时还会自动发送警报。 许多开发人员发现这些功能很有用，不过你也可以根据需要扩展和自定义遥测。

在 Visual Studio 中单击几下即可完成安装。 可以选择对遥测量进行限制，从而避免付费。 可以通过此功能对用户不多的站点进行试验和调试，或者对其进行监视。 如果认为需要前进一步，对生产站点进行监视，则可轻松地在以后提高该限制。

## <a name="before-you-start"></a>开始之前
需要：

* Visual Studio 2013 Update 3 或更高版本。 版本越高越好。
* [Microsoft Azure](http://azure.com)订阅。 如果团队或组织拥有 Azure 订阅，则所有者可以使用 [Microsoft 帐户](http://live.com)你将加入其中。

如果对以下主题感兴趣，请查看其他相关文章：

* [在运行时检测 Web 应用](app-insights-monitor-performance-live-website-now.md)
* [Azure 云服务](app-insights-cloudservices.md)

## <a name="ide"></a> 步骤 1：添加 Application Insights SDK

在解决方案资源管理器中右键单击 Web 应用，并选择“添加” > “Application Insights 遥测...”或“配置 Application Insights”。

![解决方案资源管理器的屏幕截图，其中突出显示了“添加 Application Insights 遥测”](./media/app-insights-asp-net/appinsights-03-addExisting.png)

（在 Visual Studio 2015 中，还可以使用一个选项在“新建项目”对话框中添加 Application Insights。）

转到 Application Insights 配置页：

![使用 Application Insights 页注册应用的屏幕截图](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** 选择用于访问 Azure 的帐户和订阅。

**b.** 在 Azure 中，选择要在其中查看应用数据的资源。 通常请执行以下操作：

* 使用单个应用程序的[不同组件的单个资源](app-insights-monitor-multi-role-apps.md)。 
* 为不相关的应用程序创建独立的资源。
 
如果想要设置用于存储数据的资源组或位置，请单击“配置设置”。 资源组用于控制对数据的访问。 例如，如果有多个应用构成了同一个系统的一部分，可在同一个资源组中放置这些应用的 Application Insights 数据。

**c.** 为免费数据容量限制设置一个上限，避免付费。 只要不超过特定的遥测量，Application Insights 都是免费的。 创建资源后，可以通过在门户中打开“功能 + 定价” > “数据量管理” > “每日数量上限”，来更改选择。

**d.** 单击“注册”，继续为 Web 应用配置 Application Insights。 在调试期间以及发布应用后，遥测数据将发送到 [Azure 门户](https://portal.azure.com)。

**e.** 如果不希望在进行调试时向门户发送遥测，则请直接向应用添加 Application Insights SDK，但不要在门户中配置资源。 在调试时便可以在 Visual Studio 中查看遥测数据。 稍后可以返回此配置页，或者等到部署应用后，启用[在运行时打开遥测](app-insights-monitor-performance-live-website-now.md)。


## <a name="run"></a> 步骤 2：运行应用
使用 F5 运行应用。 打开不同的页以生成一些遥测数据。

在 Visual Studio 中，将看到已记录事件的计数。

![Visual Studio 的屏幕截图。 调试期间会显示“Application Insights”按钮。](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>步骤 3：查看遥测
可在 Visual Studio 或 Application Insights Web 门户中查看遥测数据。 在 Visual Studio 中搜索遥测，以便对应用进行调试。 当系统运行时，在 Web 门户中监视性能和使用情况。 

### <a name="see-your-telemetry-in-visual-studio"></a>在 Visual Studio 中查看遥测

在 Visual Studio 中打开 Application Insights 窗口。 单击“Application Insights”按钮，或在解决方案资源管理器中右键单击项目，并单击“搜索实时遥测数据”。

在 Visual Studio Application Insights 搜索窗口中，查看“来自调试会话的数据”视图，其中显示了在应用的服务器端生成的遥测数据。 使用这些筛选器进行试验，并单击任何事件以查看更多详细信息。

![Application Insights 窗口中“来自调试会话的数据”视图的屏幕截图。](./media/app-insights-asp-net/55.png)

> [!NOTE]
> 如果看不到任何数据，请确保时间范围正确，并单击“搜索”图标。

[了解有关 Visual Studio 中的 Application Insights Tools 的详细信息](app-insights-visual-studio.md)。

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>在 Wb 门户中查看遥测

还可以在 Application Insights Web 门户中查看遥测（除非选择仅安装 SDK）。 该门户中的图表、分析工具和跨组件视图比 Visual Studio 中的多。 此门户还提供警报。

打开 Application Insights 资源。 登录到 [Azure 门户](https://portal.azure.com/)并在其中找到该资源，或者在 Visual Studio 中右键单击项目转到相应的位置。

![Visual Studio 的屏幕快照，显示如何打开 Application Insights 门户](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> 如果收到访问错误：你是否有多个 Microsoft 凭据集？是否登录时使用了错误的凭据集？ 请在门户中注销，并再次登录。

门户将从应用打开遥测视图。

![Application Insights 概述页的屏幕截图](./media/app-insights-asp-net/66.png)

在门户中，单击任何磁贴或图表以查看更多详细信息。

[了解有关在 Azure 门户中使用 Application Insights 的详细信息](app-insights-dashboards.md)。

## <a name="step-4-publish-your-app"></a>步骤 4：发布应用
将应用发布到 IIS 服务器或 Azure。 监视 [实时指标流](app-insights-metrics-explorer.md#live-metrics-stream) ，确保一切平稳运行。

遥测数据会在 Application Insights 门户中累积，可在此监视指标、搜索遥测数据以及设置[仪表板](app-insights-dashboards.md)。 还可以使用功能强大的 [Log Analytics 查询语言](https://docs.loganalytics.io/)来分析使用情况和性能，或查找特定的事件。

还可以继续在 [Visual Studio](app-insights-visual-studio.md) 中借助诊断搜索和[趋势](app-insights-visual-studio-trends.md)等工具来分析遥测。

> [!NOTE]
> 如果应用发送的遥测足够达到[限制](app-insights-pricing.md#limits-summary)，自动[采样](app-insights-sampling.md)会打开。 采样可以减少从应用发送的遥测数量，同时为诊断保留相关数据。
>
>

## <a name="land"></a> 已经完成全部设置

祝贺你！ 已在应用中安装 Application Insights 包，并将其配置为向 Azure 中的 Application Insights 服务发送遥测。

![遥测数据移动示意图](./media/app-insights-asp-net/01-scheme.png)

接收应用遥测的 Azure 资源通过“检测密钥”进行标识。 可以在 ApplicationInsights.config 文件中找到该密钥。


## <a name="upgrade-to-future-sdk-versions"></a>升级到更高的 SDK 版本
要升级到 [SDK 的新版本](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)，请再次打开 **NuGet 包管理器**，并筛选已安装的包。 选择“Microsoft.ApplicationInsights.Web”，并选择“升级”。

如果对 ApplicationInsights.config 执行了任何自定义操作，请在升级前保存相关副本。 然后，将更改合并到新版本中。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤

### <a name="more-telemetry"></a>其他遥测数据

* **[浏览器和页面加载数据](app-insights-javascript.md)** - 在网页中插入代码片段。
* **[获取更详细的依赖关系和异常监视](app-insights-monitor-performance-live-website-now.md)** - 在服务器上安装状态监视器。
* **[为自定义事件编写代码](app-insights-api-custom-events-metrics.md)**可对用户操作进行计数、计时或度量。
* **[获取日志数据](app-insights-asp-net-trace-logs.md)** - 将日志数据与你的遥测相关联。

### <a name="analysis"></a>分析

* [在 Visual Studio 中使用 Application Insights](app-insights-visual-studio.md)<br/>包含有关使用遥测数据进行调试、诊断搜索和钻取代码的信息。
* **[使用 Application Insights 门户](app-insights-dashboards.md)**<br/> 包含有关仪表板、功能强大的诊断和分析工具、警报、应用程序的实时依赖项映射和遥测数据导出功能的信息。
* **[分析](app-insights-analytics-tour.md)** - 功能强大的查询语言。

### <a name="alerts"></a>警报

* [可用性测试](app-insights-monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](app-insights-proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](app-insights-alerts.md)：设置这些警报可在某个指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。

### <a name="automation"></a>自动化

* [自动创建 Application Insights 资源](app-insights-powershell.md)

