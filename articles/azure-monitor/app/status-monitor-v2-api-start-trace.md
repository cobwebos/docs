---
title: Azure Application Insights 代理 API 参考
description: Application Insights 代理 API 参考。 Start-Trace。 从状态监视器和 Application Insights SDK 收集 ETW 日志。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671216"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>应用程序见解代理 API：启动应用程序见解监视跟踪

本文介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

## <a name="description"></a>描述

从无代码附加运行时收集 [ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 此 cmdlet 是运行 [PerfView](https://github.com/microsoft/perfview) 的替代方法。

收集的事件将实时输出到控制台，并保存到 ETL 文件。 可以通过 [PerfView](https://github.com/microsoft/perfview) 打开该输出 ETL 文件，以用于进一步的调查。

此 cmdlet 将一直运行到它达到超时持续时间（默认值为 5 分钟）或被手动停止 (`Ctrl + C`)。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

### <a name="how-to-collect-events"></a>如何收集事件

通常，我们会要求你收集事件，以调查未检测应用程序的原因。

在 IIS 启动时以及在应用程序启动时，无代码附加运行时将发出 ETW 事件。

若要收集这些事件，请执行以下操作：
1. 在具有管理权限的 cmd 控制台中，执行 `iisreset /stop` 以关闭 IIS 和所有 Web 应用。
2. 执行此 cmdlet
3. 在具有管理权限的 cmd 控制台中，执行 `iisreset /start` 以启动 IIS。
4. 尝试浏览到你的应用。
5. 在应用完成加载后，你可以手动停止它 (`Ctrl + C`) 或等待超时。

### <a name="what-events-to-collect"></a>要收集的事件

收集事件时，你有下面三个选择：
1. 使用开关 `-CollectSdkEvents` 收集从 Application Insights SDK 发出的事件。
2. 使用开关 `-CollectRedfieldEvents` 收集由状态监视器和 Redfield 运行时发出的事件。 在诊断 IIS 和应用程序启动时，这些日志非常有用。
3. 使用这两个开关可收集这两种事件类型。
4. 默认情况下，如果未指定任何开关，将收集这两种事件类型。


## <a name="parameters"></a>参数

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**选。** 使用此参数可设置此脚本应收集事件的时长。 默认值为 5 分钟。

### <a name="-logdirectory"></a>-LogDirectory
**选。** 使用此开关可设置 ETL 文件的输出目录。 默认情况下，此文件将在 PowerShell 模块目录中创建。 完整路径将在脚本执行期间显示。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**选。** 使用此开关可收集 Application Insights SDK 事件。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**选。** 使用此开关可从状态监视器和 Redfield 运行时收集事件。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关输出详细的日志。



## <a name="output"></a>输出


### <a name="example-of-application-startup-logs"></a>应用程序启动日志的示例
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>后续步骤

其他故障排除方法：

- 在此处查看其他故障排除步骤：https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 请查看 [API 参考](status-monitor-v2-overview.md#powershell-api-reference)，以了解你可能错过的参数。
- 如果需要更多帮助，可以通过 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) 联系我们。



 使用 Application Insights 代理执行更多操作：
 - 使用我们的指南对 Application Insights 代理进行[故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
