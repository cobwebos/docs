---
title: "针对 Web 开发人员的绩效和使用情况分析 - Azure Application Insights | Microsoft Docs"
description: "使用 Visual Studio、Application Insights 和 HockeyApp 进行开发运营"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 1f1207a5-9019-451f-bff0-c67c4685ba32
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 06d01f6fb044c4ad3b3a4d95a11a101ef01ca121


---
# <a name="developer-analytics-with-application-insights-and-hockeyapp"></a>使用 Application Insights 和 HockeyApp 进行开发人员分析


许多项目都在运转一个快速的 [DevOps](https://en.wikipedia.org/wiki/DevOps)（开发运营）周期。 它们构建和分发其应用程序、获取有关其执行方式和用户用法的反馈，然后使用这些信息进一步规划开发周期。 

若要监视用法和性能，必须用到来自实时应用程序的遥测数据以及来自用户本身的反馈。 

许多系统是从多个组件构建的：Web 服务、后端处理器或数据存储，以及在用户浏览器中或者作为手机或其他设备中的应用运行的客户端软件。 来自这些不同组件的遥测数据必须合并在一起。

某些版本以受限的方式分发给指定的测试人员；我们还组织了正式发布前的小众测试（让有限的受众测试新功能）和 A|B 测试（对备选 UI 进行并行测试）。

管理分发版以及基于多个客户端和服务器组件集成监视功能并不是一个简单的任务。 此过程是应用程序体系结构不可或缺的一部分：在未经历多次开发周期和使用适当监视工具的情况下，无法创建这种系统。

本文将探讨如何使 devOps 周期的监视层面适应该过程的其他组成部分。 

如果想要查看特定的示例，请参阅这篇[有趣的案例研究](http://aka.ms/mydrivingdocs)，其中探讨了多个客户端和服务器组件。

## <a name="a-devops-cycle"></a>DevOps 周期
Visual Studio 和开发人员分析工具提供完美集成的 devOps 体验。 例如，下面是 Web 应用程序（可能是 Java、Node.js 或 ASP.NET）的典型周期：

![Web 应用的 DevOps 周期](./media/app-insights-developer-analytics/040.png)

* 开发人员签入代码存储库，或合并到主分支。 在此图中，存储库是 Git，但它也可能是 [Team Foundation 版本控制](https://www.visualstudio.com/docs/tfvc/overview)。
* 变更将触发生成和单元测试。 生成服务可以位于 [Visual Studio Team Services 或其本地对等服务 Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview) 中。 
* 成功的生成和单元测试可以[触发自动部署](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。 Web 应用主机可以是你自己的 Web 服务器或 Microsoft Azure。 
* 来自实时应用（包括来自服务器和[来自客户端浏览器](app-insights-javascript.md)）的遥测数据发送到 [Application Insights](app-insights-overview.md)。 可以在该处分析应用的性能和使用模式。 功能强大的[搜索工具](app-insights-analytics.md)可帮助诊断任何问题。 [警报](app-insights-alerts.md)可确保在发生问题时立即收到通知。 
* 分析实时遥测数据后，可以通知下一个开发周期。

### <a name="device-and-desktop-apps"></a>设备和桌面应用
对于设备和桌面应用，周期的分发部分稍有不同，因为我们不只是上载到一两台服务器。 成功的生成和单元测试可以[触发上载到 HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)。 HockeyApp 管理如何将版本分发给测试用户团队（如果需要，还可分发给大众）。 

![设备 DevOps 周期](./media/app-insights-developer-analytics/030.png)

HockeyApp 也按以下形式收集性能和使用情况数据：

* 带屏幕截图的具体用户反馈
* 崩溃报告
* 由你编写的自定义遥测。

同样，在根据获取的反馈拟定未来的开发计划后，devOps 周期即告完成。

## <a name="setting-up-developer-analytics"></a>设置开发人员分析
对于应用程序的每个组件（移动、Web 或桌面），步骤基本上相同。 对于许多类型的应用，Visual Studio 会自动执行其中的一些步骤。

1. 将相应的 SDK 添加到应用。 对于设备应用而言，该 SDK 是 HockeyApp；对于 Web 服务而言，它是 Application Insights。 每种 SDK 根据平台的不同而异。 （也可以针对桌面应用使用其中的任一 SDK，但我们建议使用 HockeyApp）。
2. 根据所用的 SDK，在 Application Insights 或 HockeyApp 门户中注册应用。 可在此处查看来自实时应用的分析数据。 获取应用中配置的检测密钥或 ID，这样，SDK 就知道要将其遥测数据发送到何处。
3. 添加自定义代码（如果需要）来记录事件或指标，帮助诊断或者分析性能或使用情况。 可以使用许多内置的监视功能，因此，在第一个周期不需要这样做。
4. 对于设备应用：
   * 将调试生成代码上载到 HockeyApp。 可以从此处将其分发到测试用户团队。 每次上载后续生成代码后，该团队就会收到通知。
   * 当设置连续生成服务时，请创建发布定义，使用插件步骤上载到 HockeyApp。

### <a name="analytics-and-export-for-hockeyapp-telemetry"></a>HockeyApp 遥测数据的分析和导出
可以通过[设置网桥](app-insights-hockeyapp-bridge-app.md)，使用 Application Insights 的分析和连续导出功能来调查 HockeyApp 自定义遥测数据和日志遥测数据。

## <a name="next-steps"></a>后续步骤
下面是适用于不同类型的应用的详细说明：

* [ASP.NET Web 应用](app-insights-asp-net.md) 
* [Java Web 应用](app-insights-java-get-started.md)
* [Node.js Web 应用](https://github.com/Microsoft/ApplicationInsights-node.js)
* [iOS 应用](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X 应用](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android 应用](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [通用 Windows 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 和 8.1 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation 应用](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)




<!--HONumber=Jan17_HO4-->


