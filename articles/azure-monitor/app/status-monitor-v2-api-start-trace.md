---
title: Azure 状态监视器 v2 API 参考：启动跟踪 |Microsoft Docs
description: 状态监视器 v2 API 引用。 开始跟踪。 从状态监视器和 Application Insights SDK 收集 ETW 日志。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807059"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>状态监视器 v2 API：Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

本文介绍的 cmdlet 时的成员[Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)从无代码将附加的运行时。 此 cmdlet 是正在运行的替代方法[PerfView](https://github.com/microsoft/perfview)。

收集的事件将被打印到实时和保存到 ETL 文件中的控制台。 可以通过打开输出 ETL 文件[PerfView](https://github.com/microsoft/perfview)以便进一步进行调查。

此 cmdlet 将运行，直到它达到超时持续时间 （默认值为 5 分钟），或手动停止 (`Ctrl + C`)。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

### <a name="how-to-collect-events"></a>如何收集事件

通常，我们会要求你收集事件，以调查为什么您的应用程序不进行检测。

无代码将附加的运行时将发出 ETW 事件，IIS 启动时和应用程序启动时。

若要收集这些事件：
1. 在具有管理权限的 cmd 控制台中，执行 `iisreset /stop` 以关闭 IIS 和所有 Web 应用。
2. 执行此 cmdlet
3. 在具有管理权限的 cmd 控制台中，执行 `iisreset /start` 以启动 IIS。
4. 尝试浏览到你的应用。
5. 你的应用完成加载后，你可以手动停止它 (`Ctrl + C`) 或等待超时。

### <a name="what-events-to-collect"></a>若要收集哪些事件

收集事件时有三个选项：
1. 使用开关`-CollectSdkEvents`用于收集从 Application Insights SDK 发出的事件。
2. 使用开关`-CollectRedfieldEvents`收集发出的状态监视器和 Redfield 运行时事件。 诊断 IIS 时，这些日志很有用和应用程序启动。
3. 使用这两个开关来收集这两种事件类型。
4. 默认情况下，如果没有指定任何开关，将收集这两种事件类型。


## <a name="parameters"></a>Parameters

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**可选。** 此参数用于设置此脚本应多长时间收集事件。 默认值为 5 分钟。

### <a name="-logdirectory"></a>-LogDirectory
**可选。** 使用此开关设置 ETL 文件的输出目录。 默认情况下，将 PowerShell 模块目录中创建此文件。 将执行脚本期间显示的完整路径。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**可选。** 使用此开关用于收集 Application Insights SDK 的事件。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**可选。** 使用此开关从状态监视器以及 Redfield 运行时收集事件。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可输出详细日志。



## <a name="output"></a>Output


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

其他故障排除：

- 查看其他故障排除的步骤： https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 审阅[API 参考](status-monitor-v2-overview.md#powershell-api-reference)若要了解有关您可能错过的参数。
- 如果需要更多帮助，请与我们联系上, [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。



 使用状态监视器 v2 执行更多操作：
 - 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
