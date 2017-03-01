---
title: "监视移动应用的性能 - Azure Application Insights | Microsoft Docs"
description: "适用于移动应用开发人员的应用程序性能和使用情况监视。 使用 HockeyApp 和 Application Insights 进行开发运营。"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>使用 HockeyApp 和 Application Insights 进行移动分析
使用 [HockeyApp](https://hockeyapp.net/) 监视 beta 测试和已部署的移动应用和桌面应用的性能和使用情况。 使用 [Azure Application Insights](app-insights-overview.md) 监视所支持的 Web 服务和后端应用。 在 Analytics 中分析客户端应用和服务器应用的数据，并在 Azure 仪表板中并排显示图表。

Microsoft Developer Analytics 针对应用程序性能监视提供两种解决方案：

* **适用于移动应用和桌面应用的 HockeyApp**。
  * 向选定用户分发测试版本。
  * 崩溃分析。
  * 用于使用情况分析的自定义遥测。
* **适用于网站、服务和后端应用程序的 Application Insights**。
  * 性能和使用情况指标与警报。
  * 异常报告和诊断跟踪。
  * 使用筛选和相关遥测链接进行诊断搜索。

这两种解决方案都提供：

* 功能强大的**[分析查询语言](app-insights-analytics.md)**，用于诊断和分析。
* **[将数据导出](app-insights-export-telemetry.md)**到用户自己的存储。
* **集成仪表板**，显示分析图表和表。

## <a name="monitor-your-app-components"></a>监视应用组件
按照这些页中的说明在代码中安装 SDK，并开始跟踪应用。

### <a name="web-apps---application-insights"></a>Web 应用 - Application Insights
* [ASP.NET Web 应用](app-insights-asp-net.md) 
* [Java Web 应用](app-insights-java-get-started.md)
* [Node.js Web 应用](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure 云服务](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>移动应用 - HockeyApp
* [iOS 应用](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X 应用](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android 应用](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [通用 Windows 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 和 8.1 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

对于 Windows 桌面应用，我们建议使用 HockeyApp。 但也可以[从 Windows 应用将遥测数据发送到 Application Insights](app-insights-windows-desktop.md)。 你可能想要执行该操作来试验 Application Insights。

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>HockeyApp 遥测数据的分析和导出
可以通过[设置网桥](app-insights-hockeyapp-bridge-app.md)，使用 Application Insights 的分析和连续导出功能来调查 HockeyApp 自定义遥测数据和日志遥测数据。




<!--HONumber=Jan17_HO4-->


