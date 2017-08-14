---
title: "监视 Windows 桌面应用的使用情况和性能"
description: "使用 Application Insights 分析 Windows 桌面应用的使用情况和性能。"
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 6f87eaf58c9c8dcd301a6ac245119621244a1c27
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>监视 Windows 桌面应用中的使用情况和性能


[Azure Application Insights](app-insights-overview.md) 和 [HockeyApp](https://hockeyapp.net) 允许监视已部署应用程序的使用情况和性能。

> [!IMPORTANT]
> 我们建议使用 [HockeyApp](https://hockeyapp.net) 分配和监视桌面和设备应用。 借助 HockeyApp，可管理分配、实时测试和用户反馈以及监视使用情况和崩溃报表。 还可以[使用分析导出和查询遥测](app-insights-hockeyapp-bridge-app.md)。
> 
> 尽管遥测可以从桌面应用程序发送到 Application Insights，但这主要适用于调试和实验目的。
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>将遥测从 Windows 应用程序发送到 Application Insights
1. 在 [Azure 门户](https://portal.azure.com)中，[创建 Application Insights 资源](app-insights-create-new-resource.md)。 对于应用程序类型，选择 ASP.NET 应用。
2. 获取检测密钥的副本。 在刚创建的新资源的 Essentials 下拉列表中找到该密钥。 
3. 在 Visual Studio 中，编辑应用项目的 NuGet 包，并添加 Microsoft.ApplicationInsights.WindowsServer。 （或者，如果只需要逻辑 API，而无需标准遥测收集模块，则选择 Microsoft.ApplicationInsights。）
4. 在代码中设置检测密钥：
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *你的密钥* `";` 
   
    或在 ApplicationInsights.config 中设置检测密钥（如果已安装标准遥测包之一）：
   
    `<InstrumentationKey>`*你的密钥*`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，请确保它在解决方案资源管理器中的属性设置为“生成操作”=“内容”、“复制到输出目录”=“复制”。
5. [使用 API](app-insights-api-custom-events-metrics.md) 发送遥测。
6. 运行应用，并在 Azure 门户中查看所创建的资源中的遥测。

## <a name="telemetry"></a>示例代码
```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>后续步骤
* [创建仪表板](app-insights-dashboards.md)
* [诊断搜索](app-insights-diagnostic-search.md)
* [探索指标](app-insights-metrics-explorer.md)
* [编写分析查询](app-insights-analytics.md)


