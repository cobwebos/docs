---
title: Azure 应用程序 Insights 代理 API 参考
description: Application Insights 代理 API 参考。 ApplicationInsightsMonitoring。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dccd7e617174bef4a85cb6293cbcc459542310f9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899710"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights 代理 API： ApplicationInsightsMonitoring

本文介绍了一个 cmdlet，它是[ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成员。

## <a name="description"></a>描述

在目标计算机上启用 IIS 应用的无代码置备连接监视。

此 cmdlet 将修改 IIS Applicationhost.config 并设置一些注册表项。
它还将创建 applicationinsights.config. ikey 文件，该文件定义每个应用使用的检测密钥。
IIS 将在启动时加载 RedfieldModule，这会在应用程序启动时将 Application Insights SDK 插入应用程序。
重新启动 IIS 以使更改生效。

启用监视后，建议使用[实时指标](live-stream.md)来快速检查应用是否正在向我们发送遥测数据。


> [!NOTE] 
> - 若要开始，需要一个检测密钥。 有关详细信息，请参阅[创建资源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 需要你查看并接受我们的许可和隐私声明。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话和提升的执行策略。 有关详细信息，请参阅[使用提升的执行策略以管理员身份运行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用检测键映射的示例
在本示例中：
- `MachineFilter` 使用 `'.*'` 通配符匹配当前计算机。
- `AppFilter='WebAppExclude'` 提供 `null` 检测密钥。 不会检测指定的应用。
- `AppFilter='WebAppOne'` 为指定的应用指定唯一的检测密钥。
- `AppFilter='WebAppTwo'` 为指定的应用指定唯一的检测密钥。
- 最后，`AppFilter` 还使用 `'.*'` 通配符来匹配之前的规则不匹配的所有 web 应用，并分配默认的检测密钥。
- 添加空格以便于阅读。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 此参数用于提供单个检测密钥，供目标计算机上的所有应用使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数来提供多个检测密钥和每个应用使用的检测密钥的映射。
您可以通过设置 `MachineFilter`为多台计算机创建单个安装脚本。

> [!IMPORTANT]
> 按照提供规则的顺序，应用将与规则匹配。 因此，您应该首先指定最特定的规则，最后指定最常见的规则。

#### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter**是计算机或C# VM 名称所需的正则表达式。
    - ". *" 将匹配所有
    - "ComputerName" 将仅与指定了精确名称的计算机匹配。
- **AppFilter**是 IIS 站点C#名称所需的正则表达式。 可以通过运行命令[iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)获取服务器上的站点列表。
    - ". *" 将匹配所有
    - "SiteName" 将仅与指定的名称完全匹配的 IIS 站点。
- 需要使用**InstrumentationKey**来监视与前面两个筛选器匹配的应用。
    - 如果要定义排除监视的规则，请将此值留空。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**可选。** 使用此开关，检测引擎可以收集有关执行托管进程期间发生的情况的事件和消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和 SQL 命令文本。

检测引擎增加了开销，并在默认情况下处于关闭状态。

### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关在无外设安装中接受许可和隐私声明。

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
如果有 web 服务器的群集，则可能使用的是[共享配置](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入此共享配置。
此脚本将失败，并出现需要额外安装步骤的消息。
使用此开关忽略此检查并继续安装必备组件。 有关详细信息，请参阅[已知冲突-与-iis 共享配置](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关显示详细日志。

### <a name="-whatif"></a>-WhatIf 
**通用参数。** 使用此开关，无需实际启用监视即可测试和验证输入参数。

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
 - [探索指标](../../azure-monitor/app/metrics-explorer.md)来监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- [将分析](../../azure-monitor/app/analytics.md)用于更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 web 测试](monitor-web-app-availability.md)，确保网站保持活动。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常并启用跟踪调用。
- [将 APPLICATION INSIGHTS SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)中，以便可以插入跟踪和日志调用。
 
 Application Insights 代理中执行更多操作：
 - 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认正确地记录了设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
