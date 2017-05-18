---
title: "智能检测 - 性能异常 | Microsoft 文档"
description: "Application Insights 执行应用遥测的智能分析，并在有潜在问题时发出警告。 此功能不需要任何设置。"
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 080b732d78731e6281e82ddc28a3e84ae8755ffa
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="smart-detection---performance-anomalies"></a>智能检测 - 性能异常

[Application Insights](app-insights-overview.md) 可自动分析 Web 应用程序的性能，并在出现潜在问题时发出警告。 我们会通过邮件将智能检测通知发送给你。

不需要对此功能进行任何特殊设置，只需在应用中配置 Application Insights 即可（在 [ASP.NET](app-insights-asp-net.md)、Java(app-insights-java-get-started.md) 或 [Node.js](app-insights-nodejs.md) 和[网页代码](app-insights-javascript.md)中）。 在应用生成足够多的遥测数据后，此功能将会激活。

## <a name="when-would-i-get-a-smart-detection-notification"></a>我何时会收到智能检测通知？

Application Insights 已根据以下依据之一检测到你的应用程序出现性能下降：

* **响应时间延长** - 应用响应请求的速度比平时要慢。 这种变化可能是在瞬间发生的，例如，最新的部署中出现回归。 也有可能是逐渐发生的，原因可能是内存泄漏。 
* **依赖项持续时间延长** - 应用对 REST API、数据库或其他依赖项发出调用。 依赖项的响应速度比平时要慢。
* **性能模式变慢** - 应用似乎出现了只会影响某些请求的性能问题。 例如，页面在某种浏览器中的加载速度比在其他浏览器中要慢，或者通过某个特定服务器处理请求的速度较慢。 目前，我们的算法会分析页面加载时间、请求响应时间和依赖项响应时间。  

智能检测需要至少 8 天的、具有适当数量的遥测数据，才能建立正常性能的基准。 因此，在运行应用程序这么长时间后，任何严重的问题都会导致发出通知。


## <a name="does-my-app-definitely-have-a-problem"></a>收到通知是否意味着我的应用肯定有问题？

不是，通知并不意味着应用肯定有问题。 这只是关于你可能需要密切关注的某些内容的建议。

## <a name="how-do-i-fix-it"></a>如何解决问题？

通知包含诊断信息。 下面是一个示例：


![下面是“服务器响应时间延长”检测的示例](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **会审**。 通知将显示有多少用户或多少操作受到影响。 这可以帮助你对问题分配优先级。
2. **范围**。 该问题是影响所有流量，还是只影响某些页面？ 它是否只出现在特定的浏览器或位置中？ 可以从通知中获取此信息。
3. **诊断**。 通常，通知的诊断信息会提示问题的性质。 例如，如果请求率较高时响应速度变慢，则表示服务器或依赖项过载。 

    否则，可在 Application Insights 中打开“性能”边栏选项卡， 在其中可以找到[探查器](app-insights-profiler.md)数据。 如果引发了异常，你还可以尝试[快照调试器](app-insights-snapshot-debugger.md)。



## <a name="configure-email-notifications"></a>配置电子邮件通知

智能检测通知默认已启用，将发送到[对 Application Insights 资源拥有所有者、参与者和读取者访问权限](app-insights-resources-roles-access-control.md)的用户。 若要更改此配置，请在电子邮件通知中单击“配置”，或者在 Application Insights 中打开“智能检测”设置。 
  
  ![智能检测设置](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * 智能检测电子邮件中也包含了“取消订阅”链接 

每天只会针对每个 Application Insights 资源发送一封有关智能检测性能异常的电子邮件。 只有当天至少检测到一个新问题时，才会发送电子邮件。 你将不会收到任何重复的消息。 

## <a name="faq"></a>常见问题

* *所以你们会查看我的数据？*
  * 否。 该服务完全是自动的。 只有你会收到通知。 你的数据是[私有](app-insights-data-retention-privacy.md)数据。
* *是否分析由 Application Insights 收集的所有数据？*
  * 目前不会。 目前，我们分析请求响应时间、依赖项响应时间和页面加载时间。 其他指标的分析功能正在规划中，今后有望推出。

* 此功能适用于哪些类型的应用程序？
  * 在生成相应遥测数据的任何应用程序中都可以检测到这些降级。 如果你已在 Web 应用中安装 Application Insights，则可以自动跟踪请求和依赖项。 但在后端服务或其他应用中，如果插入 [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) 或 [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency) 调用，智能检测将以相同的方式工作。

* *是否可以创建自己的异常检测规则或自定义现有的规则？*

  * 目前不可以，但可以：
    * [设置警报](app-insights-alerts.md)，以便在指标超出阈值时告知用户。
    * [将遥测导出到](app-insights-export-telemetry.md)[数据库](app-insights-code-sample-export-sql-stream-analytics.md)或 [PowerBI](app-insights-export-power-bi.md)，可自行在其中进行分析。
* *执行分析的频率是多少？*

  * 我们每天针对前一天（UTC 时区整天）的遥测数据运行分析。
* *那么这是否会替换[指标警报](app-insights-alerts.md)？*
  * 不能。  我们不确定检测用户视为异常的每个行为。


* *如果不执行任何操作来响应通知，是否会收到提醒？*
  * 否，你仅会收到关于每个问题的消息一次。 如果问题持续出现，它将在智能检测的源边栏选项卡中更新。
* *我丢失了电子邮件。在哪里可以找到门户中的通知？*
  * 在应用的 Application Insights 概述中，单击“智能检测”磁贴。 在该磁贴中可以找到过去最长 90 天的所有通知。

## <a name="how-can-i-improve-performance"></a>如何提高性能？
正如你从自己的经验所知，缓慢和失败的响应对于网站用户而言是最大的困扰之一。 因此，必须解决问题。

### <a name="triage"></a>会审
首先，这是否很重要？ 如果某个页面的加载速度总是很慢，但仅 1% 的站点用户曾需要查看它，或许你可以考虑更重要的事情。 另一方面，如果只有 1% 的用户打开该页面，但它每次都引发异常，这可能值得调查。

使用影响声明（受影响的用户或流量百分比）作为一般原则，但请注意这并非全部。 收集其他证据以确认。

考虑问题的参数。 如果它是地理位置相关的，请设置包括该区域在内的[可用性测试](app-insights-monitor-web-app-availability.md)：可能仅该区域存在网络问题。

### <a name="diagnose-slow-page-loads"></a>诊断缓慢页面加载
问题在哪儿？ 是服务器响应缓慢，页面过长，还是浏览器需要执行大量工作才能显示它？

打开“浏览器”指标边栏选项卡。 浏览器页面加载时间的分段显示会显示时间的进展如何。 

* 如果**发送请求时间**过高，则服务器响应速度缓慢，或者请求是包含大量数据的 post 请求。 查看[性能指标](app-insights-web-monitor-performance.md#metrics)以调查响应时间。
* 设置[依赖项跟踪](app-insights-asp-net-dependencies.md)以查看缓慢是否由于外部服务或数据库引起的。
* 如果**接收响应**占主导地位，则页面及其依赖部分（JavaScript、CSS、图像等（但并非异步加载的数据））较长。 设置[可用性测试](app-insights-monitor-web-app-availability.md)，并确保设置用于加载依赖部分的选项。 当获得一些结果时，打开某一结果的详细信息，然后展开它以查看不同文件的加载时间。
* 高**客户端处理时间**表明脚本运行缓慢。 如果原因并不明显，请考虑添加一些计时代码并发送 trackMetric 调用中的时间。

### <a name="improve-slow-pages"></a>改进缓慢的页面
存在其上充满关于改进服务器响应和页面加载时间的建议的 Web，因此我们不尝试在此处全都重复一遍。 下面是一些你可能已经知道的提示，仅供考虑：

* 由于文件较大使得加载缓慢：以异步方式加载脚本和其他部分。 使用脚本捆绑。 将主页拆分为单独加载其数据的多个小组件。 对于长表格，不要发送普通旧 HTML：使用脚本以 JSON 或其他紧凑格式请求数据，然后适当地填充表格。 有很好的框架，可以帮助实现所有这些目标。 （当然，它们还需要大脚本。）
* 缓慢的服务器依赖项：考虑组件的地理位置。 例如，如果使用的是 Azure，请确保 Web 服务器和数据库位于同一区域。 查询检索的信息是否超出自己所需的信息？ 缓存或批处理是否有帮助？
* 容量问题：查看响应时间和请求计数的服务器指标。 如果响应时间的峰值与请求计数的峰值不成比例，则服务器很有可能是外延式。


## <a name="server-response-time-degradation"></a>服务器响应时间延长

响应时间延长通知提供以下信息：

* 与此操作的正常响应时间相比的响应时间。
* 受影响的用户数量。
* 检测当天和 7 天前此操作的平均响应时间与第 90 百分位响应时间。 
* 检测当天和 7 天前此操作的请求计数。
* 此操作的降级与相关依赖项的降级之间的关联。 
* 帮助你诊断问题的链接。
  * 帮助你查看占用操作时间的位置的探查器跟踪（如果在检测期间已收集此操作的探查器跟踪示例，则会提供链接）。 
  * 指标资源管理器中的性能报告，可在其中分解此操作的时间范围/筛选器。
  * 搜索此调用以查看特定的调用属性。
  * 失败报告 - 如果计数大于 1，则表示此操作可能由于性能下降而发生失败。

## <a name="dependency-duration-degradation"></a>依赖项持续时间延长

新型应用程序越来越多地采用微服务设计方案，在许多情况下这会导致严重依赖于外部服务。 例如，如果应用程序依赖于某个数据平台，或者，即使你的机器人服务是由你自己构建的，它也可能会依赖于某个认知服务提供程序来使机器人能够以更加类似于人类的方式交互，并依赖于某个数据存储服务来让机器人提取解答。  

依赖项降级通知的示例：

![下面是“依赖项持续时间延长”检测的示例](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

请注意，它会指示：

* 与此操作的正常响应时间相比的持续时间
* 受影响的用户数量
* 检测当天和 7 天前此依赖项的平均持续时间与第 90 百分位持续时间
* 检测当天和 7 天前的依赖项调用数
* 帮助你诊断问题的链接
  * 指标资源管理器中为此依赖项提供的性能报告
  * 搜索此依赖项调用以查看调用属性
  * 失败报告 - 如果计数大于 1，则表示在检测期间可能由于持续时间延长而导致依赖项调用失败 
  * 打开 Analytics，其中包含用于计算此依赖项持续时间和计数的查询  

## <a name="smart-detection-of-slow-performing-patterns"></a>智能检测执行模式速度缓慢问题 

Application Insights 可以找到只会影响一部分用户，或者只会在某些情况下影响用户的性能问题。 例如，如果页面在某种浏览器中的加载速度比在其他浏览器中要慢，或者通过特定服务器处理请求时速度较慢，则它会发出通知。 它还可以发现与属性组合关联的问题，例如在某个地理区域中使用特定操作系统的客户端上加载页面的速度缓慢。  

仅通过检查数据，检测类似于这样的异常会很困难，但它们比你想象的更普遍。 通常仅在收到客户投诉时，它们才会显现出来。 到那时就为时已晚：受影响用户已转用你的竞争对手的产品！

目前，我们的算法关注页面加载时间、服务器上的请求响应时间和依赖项响应时间。  

你不必设置任何阈值或配置规则。 机器学习和数据挖掘算法用于检测异常模式。

![从电子邮件警报，单击链接以在 Azure 中打开诊断报告](./media/app-insights-proactive-performance-diagnostics/03.png)

* **When** 显示检测到问题的时间。
* **What** 描述：

  * 检测到的问题；
  * 我们发现显示问题行为的事件集的特征。
* 表格会将性能不佳的事件集与所有其他事件的平均行为进行比较。

单击这些链接以在相关报告上打开指标资源管理器和搜索，按缓慢执行集的时间和属性进行筛选。

修改时间范围和筛选器以浏览遥测。

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助你检查应用中的遥测：

* [探查器](app-insights-profiler.md) 
* [快照调试器](app-insights-snapshot-debugger.md)
* [分析](app-insights-analytics-tour.md)
* [分析智能诊断](app-insights-analytics-diagnostics.md)

智能检测是完全自动执行的。 但或许你想要设置更多一些警报？

* [手动配置的指标警报](app-insights-alerts.md)
* [可用性 Web 测试](app-insights-monitor-web-app-availability.md)
