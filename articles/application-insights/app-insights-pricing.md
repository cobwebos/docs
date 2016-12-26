---
title: "管理 Application Insights 的功能和数据量 | Microsoft Docs"
description: "在 Application Insights 中管理遥测量并监视成本。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3fdb050d1d6746313b2dffb089d56b71908335d2
ms.openlocfilehash: 585e493b2642b3ba798977528f7564674a44d738


---
# <a name="manage-features-and-data-volume-in-application-insights"></a>在 Application Insights 中管理功能和数据量


[Azure Application Insights][启动] 的定价以每个应用程序的数据量为基础。 如果开发期间的应用使用率或小型应用的使用率较低，则可能不会产生费用，因为每月会提供一定的免费遥测数据。

每个 Application Insights 资源作为独立服务计费，并在 Azure 订阅的账单中产生相应费用。

目前有两种定价计划。 默认的计划称为基本定价计划。 可以选择按日计费的企业计划，但会启用某些其他功能，例如[连续导出](app-insights-export-telemetry.md)。

[请参阅定价计划][定价]。

## <a name="review-pricing-plan-for-your-application-insights-resource"></a>查看 Application Insights 资源的定价计划
在应用程序的 Application Insights 资源中打开“功能 + 定价”边栏选项卡。

![选择“定价”。](./media/app-insights-pricing/01-pricing.png)

1. 查看当月数据量。 这包括接收和保留的所有数据（在通过服务器和客户端应用以及可用性测试进行[采样](app-insights-sampling.md)后）。
2. 会单独对[多步骤 Web 测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)计费。 （这不包括简单可用性测试，它已包括在数据量费用中。）
3. 启用企业计划提供的其他功能。 此计划中不含免费数据量。
4. 单击数据管理选项设置每日上限或设置引入采样。

Application Insights 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)（Azure 计费门户）查看 Azure 账单的详细信息。 

![在侧边菜单上，选择“计费”。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>数据速率
发送数据时存在三种数量限值：

* 每日上限。 默认设置为 100 GB/每日。 当应用达到上限时，我们将发送一封电子邮件，并丢弃超出的数据，直到当天结束。 可通过数据量管理边栏选项卡对其进行更改。
* [采样](app-insights-sampling.md)。 此机制可减少从服务器和客户端应用发送的遥测量，同时最大程度减小指标失真。
* “限制”会对每分钟的数据速率设限。 对于基本定价计划，限制为 5 分钟内平均 200 个数据点/秒，对于企业计划，为 1 分钟内平均 500点/秒。 

对于限制，三个存储桶各自单独计算：

* [TrackTrace 调用](app-insights-api-custom-events-metrics.md#track-trace)和[捕获日志](app-insights-asp-net-trace-logs.md)
* [异常](app-insights-api-custom-events-metrics.md#track-exception)，限制为 50 点/秒。
* 所有其他遥测（页面视图、会话、请求、依赖项、指标、自定义事件、web 测试结果）。

*如果应用超过每秒速率，会发生什么情况？*

* 每分钟会评估一次应用发送的数据量。 如果超出一分钟内的平均每秒速率，服务器将拒绝某些请求。 SDK 会缓冲数据，然后尝试重新发送，导致数分钟内数据传输量激增。 如果应用连续以超出限制的速率发送数据，一些数据将被丢弃。 （ASP.NET、Java 和 JavaScript Sdk 会尝试以这种方式重新发送；其他 SDK 可能会丢弃超出限制的数据。）

如果发生超出限制的情况，将会显示一个通知，警告发生了这种情况。

*如何知晓应用发送了多少数据点？*

* 打开“定价”边栏选项卡，查看“数据量”图表。
* 或在“指标资源管理器”中，添加新图表并选择“数据点量”作为其指标。 启用“分组”，并按**数据类型**分组。

## <a name="to-reduce-your-data-rate"></a>降低数据速率
可通过以下操作降低数据量：

* 降低每日数据量上限。 默认值为 100GB/日。
* 使用[采样](app-insights-sampling.md)。 此技术可减少数据速率，同时不会影响指标的准确性，也不会中断在“搜索”中的相关项目之间导航的能力。 在服务器应用中，它会自动运行。
* [限制可在每个页面视图中报告的 Ajax 调用数](app-insights-javascript.md#detailed-configuration)或关闭 Ajax 报告。
* 通过[编辑 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 关闭不需要的集合模块。 例如，用户可能认为不再需要性能计数器或依赖项数据。
* 将遥测拆分到单独的检测密钥。 
* 预先聚合指标。 如果已将对 TrackMetric 的调用放在应用中，则可通过使用重载降低流量，这种重载接受对一批度量值的平均偏差和标准偏差的计算结果。 也可使用 [预先聚合包](https://www.myget.org/gallery/applicationinsights-sdk-labs)。 

## <a name="sampling"></a>采样
[采样](app-insights-sampling.md)是一种方法，可降低向应用发送遥测的速率，同时仍可在诊断搜索过程中查找相关事件，并保留正确的事件计数。 

采样是降低费用同时又不超出每月配额的有效方式。 采样算法会保留遥测的相关项，这样，当使用“搜索”时便可查找与特定异常相关的请求。 该算法还保留正确计数，使用户可在指标资源管理器中看到请求率、异常率和其他计数的正确值。

有数种形式的采样。

* [自适应采样](app-insights-sampling.md)是用于 ASP.NET SDK 时的默认设置，它可自动调整为应用发送的遥测量。 它会在 Web 应用的 SDK 中自动运行，以便减少网络上的遥测流量。 
* *引入采样*是一种替代方法，会在应用的遥测进入 Application Insights 服务时运行。 这种形式的采样不会影响从应用发送的遥测量，但会减少服务保留的量。 可以使用这种采样来降低来自浏览器和其他 SDK 的遥测所使用的配额。

若要设置引入采样，请在定价边栏选项卡中设置控件：

![单击“配额和定价”边栏选项卡中的“示例”磁贴，然后选择采样分数。](./media/app-insights-pricing/04.png)

> [!WARNING]
> “保留的样本”磁贴上显示的值仅指示为引入采样设置的值。 它不会显示应用中 SDK 处运行的采样率。 
> 
> 如果传入的遥测已在 SDK 处进行了采样，则不适用引入采样。
> 
> 

若要查找实际采样率（无论是否已应用），请使用如下所示的[分析查询](app-insights-analytics.md)：

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

在每个保留的记录中，`itemCount` 指示它表示的原始记录数，其等于 1 +（即以前已放弃的记录数）。 


## <a name="limits-summary"></a>限制摘要
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>后续步骤

* [采样](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[启动]: app-insights-overview.md
[定价]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Nov16_HO3-->


