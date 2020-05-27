---
title: 使用 Powershell 在 Application Insights 中设置警报 | Microsoft 文档
description: 自动配置 Application Insights，以获取有关指标更改的电子邮件。
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: f35658b08eff7574448e3c72b103178b66acbbe0
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701831"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>使用 PowerShell 在 Application Insights 中设置警报

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

可以在 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 中自动配置[警报](../../azure-monitor/platform/alerts-log.md)。

此外，可以[将 webhook 设置为自动执行对警报的响应](../../azure-monitor/platform/alerts-webhooks.md)。

> [!NOTE]
> 如果要同时创建资源和警报，请考虑[使用 Azure 资源管理器模板](powershell.md)。

## <a name="one-time-setup"></a>一次性设置
如果之前尚未将 PowerShell 与 Azure 订阅结合使用：

在要运行脚本的计算机上安装 Azure Powershell 模块。

* 安装 [Microsoft Web 平台安装程序（v5 或更高版本）](https://www.microsoft.com/web/downloads/platform.aspx)。
* 使用它来安装 Microsoft Azure Powershell

## <a name="connect-to-azure"></a>连接到 Azure
启动 Azure PowerShell 并[连接到订阅](/powershell/azure/overview)：

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>获取警报
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>添加警报
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>示例 1
如果服务器对 HTTP 请求的响应（平均值超过 5 分钟）慢于 1 秒，请向我发送电子邮件。 我的 Application Insights 资源名称为 IceCreamWebApp，并且位于资源组 Fabrikam 中。 我是 Azure 订阅的所有者。

GUID 是订阅 ID（不是应用程序的检测密钥）。

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>示例 2
我在应用程序中使用 [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) 报告名为“salesPerHour”的指标。 如果“salesPerHour”低于 100（平均值超出 24 小时），会向我的同事发送电子邮件。

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

同一规则可用于通过使用另一跟踪调用（如 TrackEvent 或 trackPageView）的[测量参数](../../azure-monitor/app/api-custom-events-metrics.md#properties)报告的指标。

## <a name="metric-names"></a>指标名称
| 指标名称 | 屏幕名称 | 说明 |
| --- | --- | --- |
| `basicExceptionBrowser.count` |浏览器异常 |浏览器中所引发未捕获异常的计数。 |
| `basicExceptionServer.count` |服务器异常 |应用所引发未经处理的异常的计数 |
| `clientPerformance.clientProcess.value` |客户端处理时间 |从接收文档的最后一个字节到 DOM 加载完之间的时间。 可能仍在处理异步请求。 |
| `clientPerformance.networkConnection.value` |页面加载网络连接时间 |浏览器连接到网络所需的时间。 如果已缓存，可以为 0。 |
| `clientPerformance.receiveRequest.value` |接收响应时间 |从浏览器发送请求到开始接收响应之间的时间。 |
| `clientPerformance.sendRequest.value` |发送请求时间 |浏览器发送请求所需的时间。 |
| `clientPerformance.total.value` |浏览器页面加载时间 |从用户请求一直到 DOM、样式表、脚本和映像加载完之间的时间。 |
| `performanceCounter.available_bytes.value` |可用内存 |可立即供进程或系统使用的物理内存。 |
| `performanceCounter.io_data_bytes_per_sec.value` |进程 IO 率 |每秒读取和写入文件、网络和设备的总字节数。 |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |异常率 |每秒引发的异常。 |
| `performanceCounter.percentage_processor_time.value` |进程 CPU |由处理器使用所有进程线程针对应用程序进程执行指令所花费的时间百分比。 |
| `performanceCounter.percentage_processor_total.value` |处理器时间 |处理器在非空闲线程上所花费的时间百分比。 |
| `performanceCounter.process_private_bytes.value` |进程专用字节 |以独占方式分配给受监视应用程序进程的内存。 |
| `performanceCounter.request_execution_time.value` |ASP.NET 请求执行时间 |最近的请求执行时间。 |
| `performanceCounter.requests_in_application_queue.value` |执行队列中的 ASP.NET 请求 |应用程序请求队列的长度。 |
| `performanceCounter.requests_per_sec.value` |ASP.NET 请求速率 |每秒从 ASP.NET 发出的应用程序所有请求的速率。 |
| `remoteDependencyFailed.durationMetric.count` |依赖项失败次数 |服务器应用程序对外部资源所进行的失效调用的计数。 |
| `request.duration` |服务器响应时间 |从接收 HTTP 请求到完成响应发送之间的时间。 |
| `request.rate` |请求速率 |每秒应用程序所有请求的速率。 |
| `requestFailed.count` |失败的请求 |响应代码中生成的 HTTP 请求计数 >= 400 |
| `view.count` |页面视图 |网页的客户端用户请求的计数。 综合流量已筛选掉。 |
| {自定义指标名称} |{指标名称} |由 [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) 报告或者[跟踪调用的测量参数](../../azure-monitor/app/api-custom-events-metrics.md#properties)中的指标值。 |

指标由不同的遥测模块发送：

| 指标组 | 收集器模块 |
| --- | --- |
| basicExceptionBrowser、<br/>clientPerformance、<br/>view |[浏览器 JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[“性能”](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[依赖项](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| request、<br/>requestFailed |[服务器请求](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhook
可[自动执行对警报的响应](../../azure-monitor/platform/alerts-webhooks.md)。 引发警报时，Azure 将调用所选的 Web 地址。

## <a name="see-also"></a>另请参阅
* [用于配置 Application Insights 的脚本](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [从模板创建 Application Insights 和 Web 测试资源](powershell.md)
* [自动执行 Microsoft Azure 诊断到 Application Insights 的耦合](powershell-azure-diagnostics.md)
* [自动执行对警报的响应](../../azure-monitor/platform/alerts-webhooks.md)
