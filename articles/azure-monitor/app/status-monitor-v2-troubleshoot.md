---
title: Azure 状态监视器 v2 疑难解答和已知的问题 |Microsoft Docs
description: 状态监视器 v2 的已知问题和故障排除示例。 无需重新部署网站即可监视网站性能。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
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
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734951"
---
# <a name="troubleshooting-status-monitor-v2"></a>对状态监视器 v2 进行故障排除

时启用监视，您可能会阻止数据收集的问题。
本文列出了所有已知的问题，并提供故障排除示例。
如果你遇到此处未列出的问题，请与我们联系上, [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。


> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="known-issues"></a>已知问题

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>应用程序的 bin 目录中的冲突 Dll

如果其中任何 Dll 的 bin 目录中存在，监视可能会失败：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

这些 Dll 的一些包括在 Visual Studio 默认应用程序模板，即使您的应用程序不会使用它们。
故障排除工具可用于查看症状行为：

- PerfView：
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和应用程序的负载 （无需遥测）。 调查使用 Sysinternals （Handle.exe 和 ListDLLs.exe）：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>与 IIS 共享配置冲突

如果你拥有的 web 服务器群集，则可能使用[共享的配置](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 不能注入到此共享的配置。
若要将 DLL 安装到每个服务器的 gac 中每个 web 服务器上运行启用命令。

在运行启用命令后，完成以下步骤：
1. 转到共享的配置目录并找到 applicationHost.config 文件。
2. 将此行添加到你的配置的模块部分：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>故障排除
    
### <a name="troubleshooting-powershell"></a>PowerShell 故障排除

#### <a name="determine-which-modules-are-available"></a>确定哪些模块可用
可以使用`Get-Module -ListAvailable`命令，以确定安装哪些模块。

#### <a name="import-a-module-into-the-current-session"></a>将模块导入到当前会话
如果模块尚未加载到 PowerShell 会话，则可以手动加载它通过使用`Import-Module <path to psd1>`命令。


### <a name="troubleshooting-the-status-monitor-v2-module"></a>对状态监视器 v2 模块进行故障排除

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>列出可对状态监视器 v2 模块中的命令
运行命令`Get-Command -Module Az.ApplicationMonitor`若要获取可用命令：

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>确定当前版本的状态监视器 v2 模块
运行`Get-ApplicationInsightsMonitoringStatus`命令以显示有关该模块的以下信息：
   - PowerShell 模块版本
   - Application Insights SDK 版本
   - PowerShell 模块的文件路径
    
审阅[API 参考](status-monitor-v2-api-get-status.md)有关如何使用此 cmdlet 的详细说明。


### <a name="troubleshooting-running-processes"></a>对正在运行的进程进行故障排除

您可以检查以确定是否将加载所有 Dll 检测计算机上的进程。
监视正常运行，如果应加载至少为 12 的 Dll。

使用`Get-ApplicationInsightsMonitoringStatus -InspectProcess`命令来检查 Dll。

审阅[API 参考](status-monitor-v2-api-get-status.md)有关如何使用此 cmdlet 的详细说明。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 日志

#### <a name="setup"></a>设置

1. 下载 PerfView.exe 和从 PerfView64.exe [GitHub](https://github.com/Microsoft/perfview/releases)。
2. 启动 PerfView64.exe。
3. 展开**高级选项**。
4. 清除这些复选框：
    - **Zip**
    - **合并**
    - **.NET 符号集合**
5. 这些设置设定**其他提供程序**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集日志

1. 在具有管理员权限的命令控制台，运行`iisreset /stop`命令将其关闭 IIS 和所有 web 应用。
2. 在 PerfView 中选择**开始收集**。
3. 在具有管理员权限的命令控制台，运行`iisreset /start`命令以启动 IIS。
4. 尝试浏览到你的应用。
5. 您的应用程序加载后，返回到 PerfView，然后选择**停止收集**。



## <a name="next-steps"></a>后续步骤

- 审阅[API 参考](status-monitor-v2-overview.md#powershell-api-reference)若要了解有关您可能错过的参数。
- 如果你遇到此处未列出的问题，请与我们联系上, [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。
