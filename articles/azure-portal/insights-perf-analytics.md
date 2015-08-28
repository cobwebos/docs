<properties 
	pageTitle="监视 Azure Web 应用性能" 
	description="图表加载和响应时间、依赖项信息以及设置性能警报。" 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="azure-portal" 
	ms.date="07/17/2015" 
	wacn.date=""/>

# 监视 Azure Web 应用性能

在 [Azure 门户](http://manage.windowsazure.cn)中，您可以在 [Azure Web 应用](/documentation/articles/app-service-web-overview)或[虚拟机](/documentation/articles/virtual-machines-about)中设置监视功能，以收集有关应用程序依赖项的统计信息和详细信息。

Azure 利用*扩展*支持应用程序性能监视（或 *APM*）。这些扩展被安装到您的应用程序中，负责收集数据并反馈给监视服务。

Application Insights 和 New Relic 是可用性能监视扩展的其中两个。若要使用 New Relic，请安装运行时代理。若要使用 Application Insights，请用 SDK 重新生成您的代码；也可以安装提供其他数据访问权限的扩展。SDK 允许您编写代码来更详细地监视应用的使用情况和性能。

## 启用扩展

1. 单击“浏览”并选择想要检测的 Web 应用或虚拟机。

2. 添加 Application Insights 或 New Relic 扩展。

    如果您检测的是 Web 应用：

![设置、扩展、添加、Application Insights](./media/insights-perf-analytics/05-extend.png)

或者如果您使用的是虚拟机：

![单击“分析”磁贴](./media/insights-perf-analytics/10-vm1.png)

### 对于 Application Insights：用 SDK 重新生成

Application Insights 的工作条件是将 SDK 安装到应用中。

在 Visual Studio 中，将 Application Insights SDK 添加到您的项目。

![右键单击 Web 项目并选择“添加 Application Insights”](./media/insights-perf-analytics/03-add.png)

当系统提示您登录时，使用 Azure 帐户的凭据。

您可以通过在开发计算机中运行该应用来测试遥测功能，也可以直接继续将其重新发布。

SDK 提供一个 API，让您可以[编写自定义遥测](/documentation/articles/app-insights-api-custom-events-metrics)来跟踪使用情况。

## 浏览数据

您的应用程序使用一段时间才能生成一些遥测。

1. 然后，在 Web 应用或虚拟机分页中，您将看到扩展已安装。
2. 单击表示应用程序的行，导航到该提供程序：![单击“刷新”](./media/insights-perf-analytics/06-overview.png)

您还可以使用“浏览”来直接转到使用过的 Application Insights 组件或 New Relic 帐户。

进入分页后，例如，对于 Application Insights，您可以：
- 打开性能：

![在 Application Insights 概述分页中，单击“性能”磁贴](./media/insights-perf-analytics/07-dependency.png)

- 通过钻取查看各个请求：

![在网格中，单击依赖项以查看相关请求。](./media/insights-perf-analytics/08-requests.png)

- 下面是一个示例，说明 SQL 依赖项所用的时间量，其中包括 SQL 调用数量和相关统计信息，比如平均持续时间和标准偏差。 

![](./media/insights-perf-analytics/01-example.png)



## 后续步骤

* [监视服务运行状况度量值](/documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* [启用监视和诊断](/documentation/articles/insights-how-to-use-diagnostics)，以在服务上收集详细的高频度量值信息。
* 每当发生操作事件或度量值超出阈值时，都[接收警报通知](/documentation/articles/insights-receive-alert-notifications)。
* 使用 [Application Insights for JavaScript 应用和网页](/documentation/articles/app-insights-web-track-usage)，针对访问网页的浏览器获取相关的客户端分析。
* 利用 Application Insights [监视任何网页的可用性和响应能力](/documentation/articles/app-insights-monitor-web-app-availability)，让您可以了解网页是否运行正常。
 

<!---HONumber=67-->