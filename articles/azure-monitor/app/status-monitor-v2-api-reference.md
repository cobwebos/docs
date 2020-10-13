---
title: Azure Application Insights .Net 代理 API 参考
description: Application Insights 代理 API 参考。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2f814c54aeca8a337f786beb8da1b98accbeef7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318991"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights 代理 API 参考

本文介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

> [!NOTE] 
> - 若要开始，需要一个检测密钥。 有关详细信息，请参阅[创建资源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 要求查看并接受我们的许可条款和隐私声明。

> [!IMPORTANT] 
> 此 cmdlet 要求使用管理员权限和提升的执行策略建立 PowerShell 会话。 有关详细信息，请参阅[以管理员身份使用提升的执行策略运行 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。
> - 此 cmdlet 要求查看并接受我们的许可条款和隐私声明。
> - 检测引擎会增加额外的开销，默认情况下处于关闭状态。


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

通过设置一些注册表项启用检测引擎。
重启 IIS 以使这些更改生效。

检测引擎可以补充 .NET SDK 收集的数据。
它收集描述托管进程执行的事件和消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和 [SQL 命令文本](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

在以下情况下启用检测引擎：
- 已使用 Enable cmdlet 启用了监视，但未启用检测引擎。
- 已使用 .NET SDK 手动检测应用，并希望收集其他遥测数据。

### <a name="examples"></a>示例

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>parameters

#### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关可在无外设安装中接受许可条款和隐私声明。

#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可输出详细日志。

### <a name="output"></a>输出


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功启用检测引擎的示例输出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

对目标计算机上的 IIS 应用启用无代码附加监视。

此 cmdlet 将修改 IIS applicationHost.config 并设置一些注册表项。
它还将创建 applicationinsights.ikey.config 文件，该文件定义每个应用使用的检测密钥。
IIS 在启动时会加载 RedfieldModule，从而在应用程序启动时将 Application Insights SDK 注入到应用程序中。
重启 IIS 以使更改生效。

启用监视后，我们建议你使用[实时指标](live-stream.md)快速检查应用是否正在向我们发送遥测数据。

### <a name="examples"></a>示例

#### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>使用检测密钥映射的示例
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


### <a name="parameters"></a>parameters

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 使用此参数提供单个检测密钥以供目标计算机上的所有应用使用。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数可提供多个检测密钥以及每个应用使用的检测密钥映射。
通过设置 `MachineFilter`，可以为多台计算机创建单个安装脚本。

> [!IMPORTANT]
> 应用将按照提供规则的顺序与规则相匹配。 因此，你应该首先指定最特定的规则，最后指定最通用的规则。

##### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ComputerName”将仅匹配具有指定确切名称的计算机。
- **AppFilter** 是 IIS 站点名称所需的 C# 正则表达式。 可以通过运行命令 [get-iissite](/powershell/module/iisadministration/get-iissite) 获取服务器上的站点列表。
    - “.*”将匹配所有项
    - “SiteName”将仅匹配具有指定确切名称的 IIS 站点。
- 需要 **InstrumentationKey** 才能监视与上述两个筛选器匹配的应用。
    - 如果要定义排除监视的规则，请将此值保留为 null。


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**可选。** 使用此开关可让检测引擎收集执行托管进程期间发生的事件和相关消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和 SQL 命令文本。

检测引擎会增加开销，默认情况下处于关闭状态。

#### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关可在无外设安装中接受许可条款和隐私声明。

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
当你使用一组 Web 服务器时，你可能正在使用[共享配置](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入此共享配置。
此脚本将失败，并提示需要额外的安装步骤。
使用此开关可忽略该检查并继续安装必备组件。 有关详细信息，请参阅[已知与 iis 共享配置的冲突](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可显示详细日志。

#### <a name="-whatif"></a>-WhatIf 
**通用参数。** 使用此开关可以测试和验证输入参数，而无需真正启用监视。

### <a name="output"></a>输出

#### <a name="example-output-from-a-successful-enablement"></a>成功启用后的示例输出

```powershell
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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

通过删除一些注册表项来禁用检测引擎。
重启 IIS 以使这些更改生效。

### <a name="examples"></a>示例

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>parameters 

#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可输出详细日志。

### <a name="output"></a>输出


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功禁用检测引擎的示例输出

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

在目标计算机上禁用监视。
此 cmdlet 将删除对 IIS applicationHost.config 的编辑并删除注册表项。

### <a name="examples"></a>示例

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>parameters 

#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可显示详细日志。

### <a name="output"></a>输出


##### <a name="example-output-from-successfully-disabling-monitoring"></a>成功禁用监视的示例输出

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

获取配置文件并将值输出到控制台。

### <a name="examples"></a>示例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>parameters

不需要参数。

### <a name="output"></a>输出


##### <a name="example-output-from-reading-the-config-file"></a>读取配置文件的示例输出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

此 cmdlet 提供有关状态监视器的故障排除信息。
使用此 cmdlet 调查监视状态、PowerShell 模块的版本，以及检查正在运行的进程。
此 cmdlet 将报告监视所需的版本信息和密钥文件相关信息。

### <a name="examples"></a>示例

#### <a name="example-application-status"></a>示例：应用程序状态

运行 `Get-ApplicationInsightsMonitoringStatus` 命令来显示网站的监视状态。

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

在此示例中，
- **计算机标识符**是一个匿名 ID，用于唯一标识服务器。 如果你创建支持请求，我们需要该 ID 来查找你的服务器的日志。
- “默认网站”在 IIS 中处于“已停止”状态
- **DemoWebApp111** 已在 IIS 中启动，但尚未收到任何请求。 此报告显示没有正在运行的进程（ProcessId：找不到）。
- **DemoWebApp222** 正在运行且正受监视（已检测：是）。 根据用户配置，此站点的检测密钥 xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 匹配。
- **DemoWebApp333** 已使用 Application Insights SDK 进行手动检测。 状态监视器检测到 SDK，不会监视此站点。


#### <a name="example-powershell-module-information"></a>示例：PowerShell 模块信息

运行 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 命令可显示有关当前模块的信息：

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>示例：运行时状态

可以检查已检测计算机上的进程以查看是否已加载所有 DLL。 如果监视正常工作，则至少应加载 12 个 DLL。

运行命令 `Get-ApplicationInsightsMonitoringStatus -InspectProcess`：


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>parameters

#### <a name="no-parameters"></a>（不带参数）

默认情况下，此 cmdlet 会报告 Web 应用程序的监视状态。
使用此选项来查看是否已成功检测应用程序。
也可查看哪个检测密钥已与站点匹配。


#### <a name="-powershellmodule"></a>-PowerShellModule
可选。 使用此开关报告监视所需的 DLL 的版本号和路径。
如果需要标识任何 DLL 的版本（包括 Application Insights SDK），请使用此选项。

#### <a name="-inspectprocess"></a>-InspectProcess

可选。 使用此开关报告 IIS 是否正在运行。
它还将下载外部工具，以确定是否将必要的 DLL 加载到 IIS 运行时。


如果此过程因任何原因失败，你可以手动运行这些命令：
- iisreset.exe /status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

可选。 仅与 InspectProcess 一起使用。 使用此开关可跳过在下载其他工具之前出现的用户提示。


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

在不进行完全重新安装的情况下设置配置文件。
重启 IIS 以使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。


### <a name="examples"></a>示例

#### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>使用检测密钥映射的示例
在本示例中：
- `MachineFilter` 使用 `'.*'` 通配符匹配当前计算机。
- `AppFilter='WebAppExclude'` 提供 `null` 检测密钥。 不会检测指定的应用。
- `AppFilter='WebAppOne'` 为指定的应用分配唯一的检测密钥。
- `AppFilter='WebAppTwo'` 为指定的应用分配唯一的检测密钥。
- 最后，`AppFilter` 还使用 `'.*'` 通配符来匹配按以前规则不匹配的所有 Web 应用，并分配一个默认的检测密钥。
- 添加空格以提高可读性。

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>parameters

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 使用此参数提供单个检测密钥以供目标计算机上的所有应用使用。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数可提供多个检测密钥以及每个应用使用的检测密钥映射。
通过设置 `MachineFilter`，可以为多台计算机创建单个安装脚本。

> [!IMPORTANT]
> 应用将按照提供规则的顺序与规则相匹配。 因此，你应该首先指定最特定的规则，最后指定最通用的规则。

##### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ComputerName”将仅匹配具有指定名称的计算机。
- **AppFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ApplicationName”将仅匹配具有指定名称的 IIS 应用。
- 需要 **InstrumentationKey** 才能监视与上述两个筛选器匹配的应用。
    - 如果要定义排除监视的规则，请将此值保留为 null。


#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可显示详细日志。


### <a name="output"></a>输出

默认情况下没有输出。

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>通过 -InstrumentationKey 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>通过 -InstrumentationKeyMap 设置配置文件的示例详细输出

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

从无代码附加运行时收集 [ETW 事件](/windows/desktop/etw/event-tracing-portal)。 此 cmdlet 是运行 [PerfView](https://github.com/microsoft/perfview) 的替代方法。

收集的事件将实时输出到控制台，并保存到 ETL 文件。 可以通过 [PerfView](https://github.com/microsoft/perfview) 打开该输出 ETL 文件，以用于进一步的调查。

此 cmdlet 将一直运行到它达到超时持续时间（默认值为 5 分钟）或被手动停止 (`Ctrl + C`)。

### <a name="examples"></a>示例

#### <a name="how-to-collect-events"></a>如何收集事件

通常，我们会要求你收集事件，以调查未检测应用程序的原因。

在 IIS 启动时以及在应用程序启动时，无代码附加运行时将发出 ETW 事件。

若要收集这些事件，请执行以下操作：
1. 在具有管理权限的 cmd 控制台中，执行 `iisreset /stop` 以关闭 IIS 和所有 Web 应用。
2. 执行此 cmdlet
3. 在具有管理权限的 cmd 控制台中，执行 `iisreset /start` 以启动 IIS。
4. 尝试浏览到你的应用。
5. 在应用完成加载后，你可以手动停止它 (`Ctrl + C`) 或等待超时。

#### <a name="what-events-to-collect"></a>要收集的事件

收集事件时，你有下面三个选择：
1. 使用开关 `-CollectSdkEvents` 收集从 Application Insights SDK 发出的事件。
2. 使用开关 `-CollectRedfieldEvents` 收集由状态监视器和 Redfield 运行时发出的事件。 在诊断 IIS 和应用程序启动时，这些日志非常有用。
3. 使用这两个开关可收集这两种事件类型。
4. 默认情况下，如果未指定任何开关，将收集这两种事件类型。


### <a name="parameters"></a>parameters

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**可选。** 使用此参数可设置此脚本应收集事件的时长。 默认值为 5 分钟。

#### <a name="-logdirectory"></a>-LogDirectory
**可选。** 使用此开关可设置 ETL 文件的输出目录。 默认情况下，此文件将在 PowerShell 模块目录中创建。 完整路径将在脚本执行期间显示。


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**可选。** 使用此开关可收集 Application Insights SDK 事件。

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**可选。** 使用此开关可从状态监视器和 Redfield 运行时收集事件。

#### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可输出详细日志。



### <a name="output"></a>输出


#### <a name="example-of-application-startup-logs"></a>应用程序启动日志的示例
```powershell
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

  查看遥测：
 - [浏览指标](../platform/metrics-charts.md)，以便监视性能和使用情况。
- [搜索事件和日志](./diagnostic-search.md)以诊断问题。
- 使用[分析](../log-query/log-query-overview.md)，以便进行更高级的查询。
- [创建仪表板](./overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 Web 客户端遥测](./javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](./asp-net.md)，以便插入跟踪和日志调用。
 
 使用 Application Insights 代理执行更多操作：
 - 使用我们的指南对 Application Insights 代理进行[故障排除](status-monitor-v2-troubleshoot.md)。

