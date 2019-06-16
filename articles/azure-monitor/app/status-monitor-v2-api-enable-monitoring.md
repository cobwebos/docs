---
title: Azure 状态监视器 v2 API 参考：启用监视 |Microsoft Docs
description: 状态监视器 v2 API 引用。 启用 ApplicationInsightsMonitoring。 无需重新部署网站即可监视网站性能。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734167"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>状态监视器 v2 API：Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

本文介绍的 cmdlet 时的成员[Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

启用无代码附加在目标计算机上的 IIS 应用程序的监视。

此 cmdlet 将修改 IIS applicationHost.config 并设置一些注册表项。
它还将创建一个 applicationinsights.ikey.config 文件，其中定义了每个应用所使用的检测密钥。
IIS 将加载在启动时，会将 Application Insights SDK 注入到应用程序，如应用程序启动 RedfieldModule。
重启 IIS 以使更改生效。

启用监视后，我们建议你使用[实时指标](live-stream.md)快速检查您的应用程序如果向我们发送遥测数据。


> [!NOTE] 
> - 若要开始，你需要检测密钥。 有关详细信息，请参阅[创建资源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 需要查看并接受我们的许可协议和隐私声明语句。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话和提升权限的执行策略。 有关详细信息，请参阅[以使用提升权限的执行策略管理员身份运行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>具有单个检测密钥的示例
在此示例中，在当前计算机上的所有应用都分配单个检测密钥。

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


## <a name="parameters"></a>parameters

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
    - ComputerName 将匹配仅在计算机与指定的确切名称。
- **AppFilter**需要C#的计算机或 VM 名称的正则表达式。
    - “.*”将匹配所有项
    - 应用程序名称将匹配与指定的确切名称仅 IIS 应用程序。
- **InstrumentationKey**需启用与前面两个筛选器匹配的应用程序的监视。
    - 保留此值为 null，如果你想要定义规则以排除监视。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**可选。** 使用此开关以启用要收集事件和有关托管进程的执行期间所发生的消息的检测引擎。 这些事件和消息包括依赖项结果代码、 HTTP 谓词和 SQL 命令文本。

检测引擎增加开销，并且默认情况下处于关闭状态。

### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关可在无外设安装中接受许可条款和隐私声明。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关以显示详细的日志。

### <a name="-whatif"></a>-WhatIf 
**通用参数。** 使用此开关可以测试和验证输入参数，而无需真正启用监视。

## <a name="output"></a>输出


#### <a name="example-output-from-a-successful-enablement"></a>成功启用后的示例输出

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
- [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和记录调用。
 
 使用状态监视器 v2 执行更多操作：
 - 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
