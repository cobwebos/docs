---
title: Azure 状态监视器 v2 API 参考：设置配置 |Microsoft Docs
description: 状态监视器 v2 API 引用。 Set-ApplicationInsightsMonitoringConfig. 无需重新部署网站即可监视网站性能。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
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
ms.openlocfilehash: e63d935b3c11766c4981ffb035dd45ad4019797c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807073"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v040-alpha"></a>状态监视器 v2 API：Set-ApplicationInsightsMonitoringConfig (v0.4.0-alpha)

本文档介绍了一个 cmdlet，是的成员[Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

设置配置文件而无需执行完全重新安装操作系统。
重启 IIS 以使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。


## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>具有单个检测密钥的示例
在此示例中，在当前计算机上的所有应用将都分配单个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用检测键映射示例
在本示例中：
- `MachineFilter` 通过使用与当前计算机`'.*'`通配符。
- `AppFilter='WebAppExclude'` 提供了`null`检测密钥。 不会检测指定的应用。
- `AppFilter='WebAppOne'` 将指定的应用分配唯一的检测密钥。
- `AppFilter='WebAppTwo'` 将指定的应用分配唯一的检测密钥。
- 最后，`AppFilter`还使用`'.*'`通配符来匹配前面的规则不匹配并将分配默认检测密钥的所有 web 应用。
- 添加空格，以提高可读性。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 使用此参数由目标计算机上的所有应用程序提供单个检测密钥以供使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数提供多个检测密钥和使用的每个应用的检测密钥的映射。
可以通过设置创建多台计算机的单独的安装脚本`MachineFilter`。

> [!IMPORTANT]
> 应用程序将根据规则中提供规则的顺序匹配。 因此应首先指定的最具体的规则和最后一个最一般的规则。

#### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**需要C#的计算机或 VM 名称的正则表达式。
    - “.*”将匹配所有项
    - ComputerName 将匹配具有指定名称的计算机。
- **AppFilter**需要C#的计算机或 VM 名称的正则表达式。
    - “.*”将匹配所有项
    - 应用程序名称将与 IIS 应用仅具有指定名称匹配。
- **InstrumentationKey**需启用与前面两个筛选器匹配的应用程序监视。
    - 保留此值为 null，如果你想要定义规则以排除监视。


### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关以显示详细的日志。


## <a name="output"></a>Output

默认情况下，没有输出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>通过 -InstrumentationKey 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>通过 -InstrumentationKeyMap 设置配置文件的示例详细输出

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
 - [浏览指标](../../azure-monitor/app/metrics-explorer.md)监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)来诊断问题。
- [使用 Analytics](../../azure-monitor/app/analytics.md)获取更多高级查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常，并启用跟踪调用。
- [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和日志调用
 
 使用状态监视器 v2 执行更多操作：
 - 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
