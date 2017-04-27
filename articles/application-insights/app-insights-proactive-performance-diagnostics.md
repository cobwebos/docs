---
title: "智能检测 - 性能异常 | Microsoft 文档"
description: "Application Insights 对应用遥测数据执行主动分析，并在有潜在问题时发出警告。 此功能不需要任何设置。"
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: douge
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 843a3cafd5a00dd4b4de04a43f18d58e60a48b29
ms.lasthandoff: 04/12/2017


---
# <a name="smart-detection---performance-anomalies"></a>智能检测 - 性能异常


[Application Insights](app-insights-overview.md) 对应用遥测数据执行深入分析，并且可以在有潜在性能问题时发出警告。 由于已通过电子邮件向你发送某一智能警报，你可能正在阅读该警报。

此功能不需要任何设置，并在应用生成足够的遥测时自动激活。

## <a name="what-is-smart-detection-of-performance-anomalies"></a>什么是智能检测性能异常？
智能检测通过对应用发送到 Application Insights 的遥测数据进行分析，发现应用中的异常性能模式。

特别是，它会查找仅影响某些用户的性能问题，或仅在某些情况下影响用户的性能问题。

例如，如果应用页在某种浏览器类型的加载速度比其他浏览器类型慢很多，或者如果通过特定服务器处理情求的速度较慢，它会通知你。 例如，它还可以发现与属性组合关联的问题，例如在一天内某一特定时段某个地理区域中页面加载缓慢。

仅通过检查数据，检测类似于这样的异常会很困难，但它们比你想象的更普遍。 通常仅在收到客户投诉时，它们才会显现出来。 到那时就为时已晚：受影响用户已转用你的竞争对手的产品！

目前，我们的算法关注页面加载时间、服务器上的请求响应时间和依赖项响应时间。  

你不必设置任何阈值或配置规则。 机器学习和数据挖掘算法用于检测异常模式。

## <a name="about-the-smart-detection-alert"></a>关于智能检测警报
* *为什么会收到这封电子邮件？*
  * 智能检测已对应用程序发送到 Application Insights 的遥测数据进行分析，并且已检测应用程序中的性能问题。
* *通知是否表示肯定存在问题？*
  * 否。 这只是关于你可能需要密切关注的某些内容的建议。
* *我该怎么办？*
  * [查看显示的数据](#responding-to-an-alert)。 使用指标资源管理器来查看一段时间内性能并钻取到其他指标。 使用“搜索”筛选特定事件，以帮助标识根本原因。
* *所以你们会查看我的数据？*
  * 否。 该服务完全是自动的。 只有你会收到通知。 你的数据是[私有](app-insights-data-retention-privacy.md)数据。

## <a name="the-detection-process"></a>检测过程
* *检测哪些类型的性能异常？*
  * 你会发现自行检查很耗时的模式。 例如，位置、时间和平台的特定组合的不良性能。
* *是否分析由 Application Insights 收集的所有数据？*
  * 目前不会。 目前，我们分析请求响应时间、依赖项响应时间和页面加载时间。 即将推出其他指标的分析。
* *是否可以创建自己的异常检测规则？*

  * 还不可以。 但可以：
  * [设置警报](app-insights-alerts.md)，以便在指标超出阈值时告知用户。
  * [将遥测导出到](app-insights-export-telemetry.md)[数据库](app-insights-code-sample-export-sql-stream-analytics.md)或 [PowerBI](app-insights-export-power-bi.md)，可自行在其中进行分析。
* *执行分析的频率是多少？*

  * 我们每天在前一天的遥测上运行分析。
* *那么这是否会替换[指标警报](app-insights-alerts.md)？*
  * 不会。  我们不确定检测用户视为异常的每个行为。

## <a name="how-to-investigate-the-issues-raised"></a>如何调查引发的问题
从电子邮件或从异常列表打开诊断报告。

![从电子邮件警报，单击链接以在 Azure 中打开诊断报告](./media/app-insights-proactive-performance-diagnostics/03.png)

* **When** 显示检测到问题的时间。
* **What** 描述：

  * 检测到的问题；
  * 我们发现显示问题行为的事件集的特征。
* 表格会将性能不佳的事件集与所有其他事件的平均行为进行比较。

单击这些链接以在相关报告上打开指标资源管理器和搜索，按缓慢执行集的时间和属性进行筛选。

修改时间范围和筛选器以浏览遥测。

## <a name="how-can-i-improve-performance"></a>如何提高性能？
正如你从自己的经验所知，缓慢和失败的响应对于网站用户而言是最大的困扰之一。 因此解决此类问题很重要。

### <a name="triage"></a>会审
首先，这是否很重要？ 如果某个页面的加载速度总是很慢，但仅 1% 的站点用户曾需要查看它，或许你可以考虑更重要的事情。 另一方面，如果只有 1% 的用户打开该页面，但它每次都引发异常，这可能值得调查。

使用电子邮件中的影响声明作为一般原则，但须注意这并非全部。 收集其他证据以确认。

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

## <a name="notification-emails"></a>通知电子邮件
* *是否必须订阅此服务才能接收通知？*
  * 否。 我们的自动程序会定期调查来自所有 Application Insights 用户的数据，并在检测到问题时发送通知。
* *是否可以取消订阅或者获取已发送至同事的通知？*

  * 单击警报或电子邮件中的取消订阅链接。

    当前，它们已发送至对 [Application Insights 资源具有写入权限](app-insights-resources-roles-access-control.md)的用户。

    还可以在“智能检测”边栏选项卡中编辑收件人列表设置。
* *我不希望被这些消息所淹没。*
  * 它们限制为针对我们尚未报告过的最相关的问题每天一个。 你将不会收到任何重复的消息。
* *如果我不执行任何操作，是否会收到提醒？*
  * 否，你仅会收到关于每个问题的消息一次。
* *我丢失了电子邮件。在哪里可以找到门户中的通知？*
  * 在应用的 Application Insights 概述中，单击“智能检测”磁贴。 在该磁贴中将能够找回最多 7 天的所有通知。

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助你检查应用中的遥测：

* [指标资源管理器](app-insights-metrics-explorer.md)
* [搜索资源管理器](app-insights-diagnostic-search.md)
* [分析 - 功能强大的查询语言](app-insights-analytics-tour.md)

智能检测是完全自动执行的。 但或许你想要设置更多一些警报？

* [手动配置的指标警报](app-insights-alerts.md)
* [可用性 Web 测试](app-insights-monitor-web-app-availability.md)

