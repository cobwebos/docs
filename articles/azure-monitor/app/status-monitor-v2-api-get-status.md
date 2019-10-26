---
title: Azure 应用程序 Insights 代理 API 参考
description: Application Insights 代理 API 参考。 ApplicationInsightsMonitoringStatus。 监视网站性能而不重新部署网站。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9b1010404cb876ed818dd54cf527987c6cf0ffe0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899694"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Application Insights 代理 API： ApplicationInsightsMonitoringStatus

本文介绍了一个 cmdlet，它是[ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成员。

## <a name="description"></a>描述

此 cmdlet 提供有关状态监视器的疑难解答信息。
使用此 cmdlet 调查 PowerShell 模块的监视状态、版本，并检查正在运行的进程。
此 cmdlet 将报告版本信息和监视所需的关键文件的相关信息。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

### <a name="example-application-status"></a>示例：应用程序状态

运行命令 `Get-ApplicationInsightsMonitoringStatus` 以显示网站的监视状态。

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

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

在此示例中为;
- **计算机标识符**是用于唯一标识服务器的匿名 ID。 如果创建支持请求，我们将需要此 ID 来查找服务器的日志。
- IIS 中的**默认网站**已停止
- 已在 IIS 中启动**DemoWebApp111** ，但未收到任何请求。 此报告显示没有正在运行的进程（ProcessId：未找到）。
- **DemoWebApp222**正在运行并且正在监视（检测到： true）。 基于用户配置，检测密钥的 xxxxxxxxx123 是此站点的匹配项。
- 已使用 Application Insights SDK 手动检测了**DemoWebApp333** 。 状态监视器检测到 SDK，不会监视此站点。


### <a name="example-powershell-module-information"></a>示例： PowerShell 模块信息

运行命令 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 以显示当前模块的相关信息：

```
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

### <a name="example-runtime-status"></a>示例：运行时状态

您可以在检测的计算机上检查进程，查看是否加载了所有 Dll。 如果监视正常工作，则应加载至少12个 Dll。

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

## <a name="parameters"></a>parameters

### <a name="no-parameters"></a>（无参数）

默认情况下，此 cmdlet 将报告 web 应用程序的监视状态。
使用此选项可查看是否已成功检测你的应用程序。
还可以查看与站点匹配的检测密钥。


### <a name="-powershellmodule"></a>-PowerShellModule
可选。 使用此开关报告监视所需的 Dll 的版本号和路径。
如果需要确定任何 DLL （包括 Application Insights SDK）的版本，请使用此选项。

### <a name="-inspectprocess"></a>-InspectProcess

可选。 使用此开关报告 IIS 是否正在运行。
它还将下载外部工具来确定是否将必要的 Dll 加载到 IIS 运行时。


如果此过程出于任何原因而失败，则可以手动运行以下命令：
- iisreset/status
- [handle64](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp.exe |findstr/I "InstrumentationEngine AI。 Applicationinsights.config
- [listdlls64](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp.exe |findstr/I "InstrumentationEngine AI Applicationinsights.config"


### <a name="-force"></a>-Force

可选。 仅与 InspectProcess 一起使用。 使用此开关可以跳过显示其他工具之前显示的用户提示。


## <a name="next-steps"></a>后续步骤

 Application Insights 代理中执行更多操作：
 - 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
