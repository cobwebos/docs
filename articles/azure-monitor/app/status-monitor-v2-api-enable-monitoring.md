---
title: Azure 状态监视器 v2 API 参考：启用监视 |Microsoft Docs
description: 状态监视器 v2 API 参考。 ApplicationInsightsMonitoring。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
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
ms.openlocfilehash: d3963889e3604fb67cb526b992e7ca27b1212b59
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326357"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>状态监视器 v2 API：ApplicationInsightsMonitoring

本文介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

## <a name="description"></a>描述

对目标计算机上的 IIS 应用启用无代码附加监视。

此 cmdlet 将修改 IIS applicationHost.config 并设置一些注册表项。
它还将创建 applicationinsights.ikey.config 文件，该文件定义每个应用使用的检测密钥。
IIS 在启动时会加载 RedfieldModule，从而在应用程序启动时将 Application Insights SDK 注入到应用程序中。
重启 IIS 以使更改生效。

启用监视后，我们建议你使用[实时指标](live-stream.md)快速检查应用是否正在向我们发送遥测数据。


> [!NOTE] 
> - 若要开始，需要一个检测密钥。 有关详细信息，请参阅[创建资源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 要求查看并接受我们的许可条款和隐私声明。

> [!IMPORTANT] 
> 此 cmdlet 要求使用管理员权限和提升的执行策略建立 PowerShell 会话。 有关详细信息，请参阅[以管理员身份使用提升的执行策略运行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用检测密钥映射的示例
在本示例中：
- `MachineFilter` 使用 `'.*'` 通配符匹配当前计算机。
- `AppFilter='WebAppExclude'` 提供 `null` 检测密钥。 不会检测指定的应用。
- `AppFilter='WebAppOne'` 为指定的应用分配唯一的检测密钥。
- `AppFilter='WebAppTwo'` 为指定的应用分配唯一的检测密钥。
- 最后，`AppFilter` 还使用 `'.*'` 通配符来匹配按以前规则不匹配的所有 Web 应用，并分配一个默认的检测密钥。
- 添加空格以提高可读性。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 使用此参数提供单个检测密钥以供目标计算机上的所有应用使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数可提供多个检测密钥以及每个应用使用的检测密钥映射。
通过设置 `MachineFilter`，可以为多台计算机创建单个安装脚本。

> [!IMPORTANT]
> 应用将按照提供规则的顺序与规则相匹配。 因此，你应该首先指定最特定的规则，最后指定最通用的规则。

#### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ComputerName”将仅匹配具有指定确切名称的计算机。
- **AppFilter**是 IIS 站点C#名称所需的正则表达式。 可以通过运行命令[iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)获取服务器上的站点列表。
    - “.*”将匹配所有项
    - "SiteName" 将仅与指定的名称完全匹配的 IIS 站点。
- 需要 **InstrumentationKey** 才能监视与上述两个筛选器匹配的应用。
    - 如果要定义排除监视的规则，请将此值保留为 null。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**可选。** 使用此开关可让检测引擎收集执行托管进程期间发生的事件和相关消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和 SQL 命令文本。

检测引擎会增加开销，默认情况下处于关闭状态。

### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关可在无外设安装中接受许可条款和隐私声明。

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
当你使用一组 Web 服务器时，你可能正在使用[共享配置](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入此共享配置。
此脚本将失败，并提示需要额外的安装步骤。
使用此开关可忽略该检查并继续安装必备组件。 有关详细信息，请参阅[已知与 iis 共享配置的冲突](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可显示详细日志。

### <a name="-whatif"></a>-WhatIf 
**通用参数。** 使用此开关可以测试和验证输入参数，而无需真正启用监视。

## <a name="output"></a>Output


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
 - [浏览指标](../../azure-monitor/app/metrics-explorer.md)，以便监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- [使用分析](../../azure-monitor/app/analytics.md)，以便进行更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用。
 
 使用状态监视器 v2 执行更多操作：
 - 使用我们的指南可[排查状态监视器 v2 问题](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
