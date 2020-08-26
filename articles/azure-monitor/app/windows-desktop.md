---
title: 监视 Windows 桌面应用的使用情况和性能
description: 使用 Application Insights 分析 Windows 桌面应用的使用情况和性能。
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 17613fc6cea24643c2b88182e7e56a1d216b2da8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323411"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>监视经典 Windows 桌面应用中的使用情况和性能

在 Azure 和其他云中本地托管的应用程序都可以利用 Application Insights。 唯一限制是需要[允许与 Application Insights 服务通信](./ip-addresses.md)。 若要监视通用 Windows 平台 (UWP) 应用程序，我们建议使用 [Visual Studio App Center](../learn/mobile-center-quickstart.md)。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>将遥测从经典 Windows 应用程序发送到 Application Insights
1. 在 [Azure 门户](https://portal.azure.com)中，[创建 Application Insights 资源](./create-new-resource.md)。 
2. 获取检测密钥的副本。
3. 在 Visual Studio 中，编辑应用项目的 NuGet 包，并添加 Microsoft.ApplicationInsights.WindowsServer。 （如果只是想要基本 API，请选择 Applicationinsights.config，而无需标准遥测收集模块。）
4. 在代码中设置检测密钥：
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *你的密钥* `";`
   
    或在 ApplicationInsights.config 中设置检测密钥（如果已安装标准遥测包之一）：
   
    `<InstrumentationKey>`*你的密钥*`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，请确保它在解决方案资源管理器中的属性设置为“生成操作”=“内容”、“复制到输出目录”=“复制”****。
5. [使用 API](./api-custom-events-metrics.md) 发送遥测。
6. 运行应用，并在 Azure 门户中创建的资源中查看遥测。

## <a name="example-code"></a><a name="telemetry"></a>示例代码

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>替代计算机名称的存储

默认情况下，此 SDK 将收集并存储发出遥测的系统的计算机名称。

计算机名称由 Application Insights[旧企业（按节点）定价层](./pricing.md#legacy-enterprise-per-node-pricing-tier)用于内部计费。 默认情况下，如果使用遥测初始值设定项进行重写，则会 `telemetry.Context.Cloud.RoleInstance` 发送单独的属性， `ai.internal.nodeName` 该属性仍包含计算机名称值。 此值不会与 Application Insights 遥测数据存储在一起，但会在内部引入时使用，以允许与基于旧节点的计费模型向后兼容。

如果你使用的是[旧企业（按节点）定价层](./pricing.md#legacy-enterprise-per-node-pricing-tier)，只需覆盖计算机名称的存储，请使用遥测初始值设定项：

**按如下所示编写自定义 TelemetryInitializer。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

在下面的方法中实例化初始值设定项， `Program.cs` `Main()` 设置检测密钥：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>覆盖计算机名称的传输

如果你不在[旧版 Enterprise （按节点）定价层](./pricing.md#legacy-enterprise-per-node-pricing-tier)，并且想要完全禁止发送任何包含计算机名称的遥测，则需要使用遥测处理器。

### <a name="telemetry-processor"></a>遥测处理器

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

在下面的方法中实例化遥测处理器 `Program.cs` `Main()` 设置检测密钥：

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> 虽然在技术上也可以使用上面所述的遥测处理器（即使你使用的是[旧企业（按节点）定价层）](./pricing.md#legacy-enterprise-per-node-pricing-tier)，但由于无法对每个节点的定价正确区分节点，因此可能会导致过度计费。

## <a name="next-steps"></a>后续步骤
* [创建仪表板](./overview-dashboard.md)
* [诊断搜索](./diagnostic-search.md)
* [探索指标](../platform/metrics-charts.md)
* [编写分析查询](../log-query/log-query-overview.md)

