---
title: Azure 应用程序 Insights 代理 API 参考：设置配置 |Microsoft Docs
description: Application Insights 代理 API 参考。 ApplicationInsightsMonitoringConfig。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
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
ms.openlocfilehash: 2ab941b5587a8836f1e472fbce3966b12bfa1e11
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388253"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights 代理 API： ApplicationInsightsMonitoringConfig

本文档描述了一个 cmdlet，它是[ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成员。

## <a name="description"></a>描述

设置配置文件而不进行完全重新安装。
重新启动 IIS 以使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。


## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用检测键映射的示例
在本示例中：
- `MachineFilter` 使用 @no__t 通配符来匹配当前计算机。
- @no__t 提供 @no__t 1 检测密钥。 不会检测指定的应用。
- @no__t 为指定的应用程序指定唯一的检测密钥。
- @no__t 为指定的应用程序指定唯一的检测密钥。
- 最后，`AppFilter` 还使用 `'.*'` 通配符来匹配之前的规则不匹配的所有 web 应用，并分配默认的检测密钥。
- 添加空格以便于阅读。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 此参数用于提供单个检测密钥，供目标计算机上的所有应用使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数来提供多个检测密钥和每个应用使用的检测密钥的映射。
可以通过将 @no__t 设置为多台计算机来创建单个安装脚本。

> [!IMPORTANT]
> 按照提供规则的顺序，应用将与规则匹配。 因此，您应该首先指定最特定的规则，最后指定最常见的规则。

#### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**是计算机或C# VM 名称所需的正则表达式。
    - ". *" 将匹配所有
    - "ComputerName" 将仅与具有指定名称的计算机匹配。
- **AppFilter**是计算机或C# VM 名称所需的正则表达式。
    - ". *" 将匹配所有
    - "ApplicationName" 将仅与具有指定名称的 IIS 应用匹配。
- 若要启用对与前面两个筛选器匹配的应用程序的监视，则需要**InstrumentationKey** 。
    - 如果要定义排除监视的规则，请将此值留空。


### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关显示详细日志。


## <a name="output"></a>输出

默认情况下，没有输出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>通过 InstrumentationKey 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>通过 InstrumentationKeyMap 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>后续步骤

  查看遥测：
 - [探索指标](../../azure-monitor/app/metrics-explorer.md)来监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- [将分析](../../azure-monitor/app/analytics.md)用于更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 web 测试](monitor-web-app-availability.md)，确保网站保持活动。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常并启用跟踪调用。
- [将 APPLICATION INSIGHTS SDK 添加到你的代码中](../../azure-monitor/app/asp-net.md)，以便可以插入跟踪和日志调用
 
 Application Insights 代理中执行更多操作：
 - 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认正确地记录了设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
