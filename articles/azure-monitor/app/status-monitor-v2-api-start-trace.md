---
title: Azure 应用程序 Insights 代理 API 参考：启动跟踪 |Microsoft Docs
description: Application Insights 代理 API 参考。 开始跟踪。 从状态监视器和 Application Insights SDK 收集 ETW 日志。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b1c5aa34c46a20631b328abfb061dc2477150c72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389849"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights 代理 API： ApplicationInsightsMonitoringTrace

本文介绍了一个 cmdlet，它是[ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成员。

## <a name="description"></a>描述

从无代码置备附加运行时收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)。 此 cmdlet 是运行[PerfView](https://github.com/microsoft/perfview)的替代方法。

收集的事件将实时打印到控制台并保存到 ETL 文件中。 [PerfView](https://github.com/microsoft/perfview)可以打开输出 ETL 文件以进行进一步调查。

此 cmdlet 将一直运行，直到它达到超时持续时间（默认为5分钟）或手动停止（`Ctrl + C`）。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

### <a name="how-to-collect-events"></a>如何收集事件

通常，我们会要求你收集事件以调查应用程序未被检测的原因。

当 IIS 启动和应用程序启动时，无代码置备附加运行时将发出 ETW 事件。

若要收集这些事件：
1. 在具有管理员权限的 cmd 控制台中，执行 `iisreset /stop` 以关闭 IIS 和所有 web 应用。
2. 执行此 cmdlet
3. 在具有管理员权限的 cmd 控制台中，执行 `iisreset /start` 以启动 IIS。
4. 尝试浏览到您的应用程序。
5. 应用完成加载后，可以手动停止（`Ctrl + C`）或等待超时。

### <a name="what-events-to-collect"></a>要收集的事件

收集事件时有三个选项：
1. 使用开关 `-CollectSdkEvents` 收集从 Application Insights SDK 发出的事件。
2. 使用开关 `-CollectRedfieldEvents` 收集由状态监视器和 Redfield 运行时发出的事件。 当诊断 IIS 和应用程序启动时，这些日志非常有用。
3. 使用这两个开关来收集两个事件类型。
4. 默认情况下，如果未指定任何开关，则将收集两个事件类型。


## <a name="parameters"></a>parameters

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**可选。** 使用此参数可设置此脚本应收集事件的时间长度。 默认值为 5 分钟。

### <a name="-logdirectory"></a>-LogDirectory
**可选。** 使用此开关可以设置 ETL 文件的输出目录。 默认情况下，将在 PowerShell 模块目录中创建此文件。 脚本执行过程中将显示完整路径。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**可选。** 使用此开关收集 Application Insights SDK 事件。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**可选。** 使用此开关可以从状态监视器和 Redfield 运行时收集事件。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关输出详细日志。



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

其他故障排除：

- 在此处查看其他故障排除步骤： https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 查看[API 参考](status-monitor-v2-overview.md#powershell-api-reference)，了解可能缺少的参数。
- 如果需要更多帮助，可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上联系我们。



 Application Insights 代理中执行更多操作：
 - 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认正确地记录了设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
