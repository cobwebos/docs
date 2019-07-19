---
title: Azure 状态监视器 v2 疑难解答和已知问题 |Microsoft Docs
description: 状态监视器 v2 的已知问题和故障排除示例。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
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
ms.openlocfilehash: c61d54fc49ddd0a8a9ac5063c1a2a3edea66a899
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326230"
---
# <a name="troubleshooting-status-monitor-v2"></a>对状态监视器 v2 进行故障排除

启用监视时，可能会遇到阻止数据收集的问题。
本文列出了所有已知问题，并提供了故障排除示例。
如果遇到此处未列出的问题，可以通过 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) 联系我们。

## <a name="known-issues"></a>已知问题

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>应用的 bin 目录中存在冲突的 Dll

如果 bin 目录中存在以上任何 DLL，则监视可能会失败：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

其中某些 DLL 包含在 Visual Studio 的默认应用模板中，即使应用不使用它们也是如此。
可以使用故障排除工具来查看症状行为：

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

- IISReset 和应用负载（不含遥测数据）。 使用 Sysinternals（Handle.exe 和 ListDLLs.exe）进行调查：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>与 IIS 共享配置冲突

如果拥有 Web 服务器群集，则可能会使用[共享配置](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入到此共享配置中。
在每个 Web 服务器上运行 Enable 命令，以将 DLL 安装到每个服务器的 GAC 中。

在运行 Enable 命令后，请完成以下步骤：
1. 转到共享配置目录，并找到 applicationHost.config 文件。
2. 将以下行添加到配置的模块部分中：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 嵌套应用程序

我们不会在版本1.0 中检测到 IIS 中的嵌套应用程序, 我们将在[此处](https://github.com/microsoft/ApplicationInsights-Home/issues/369)跟踪此问题。

### <a name="advanced-sdk-configuration-isnt-available"></a>高级 SDK 配置不可用。

SDK 配置未向最终用户公开版本1.0 中, 我们会在[此处](https://github.com/microsoft/ApplicationInsights-Home/issues/375)跟踪此问题。

    
    
## <a name="troubleshooting"></a>疑难解答
    
### <a name="troubleshooting-powershell"></a>PowerShell 故障排除

#### <a name="determine-which-modules-are-available"></a>确定哪些模块可用
可以使用 `Get-Module -ListAvailable` 命令来确定安装了哪些模块。

#### <a name="import-a-module-into-the-current-session"></a>将模块导入到当前会话中
如果模块尚未加载到 PowerShell 会话中，你可以使用 `Import-Module <path to psd1>` 命令手动加载该模块。


### <a name="troubleshooting-the-status-monitor-v2-module"></a>对状态监视器 v2 模块进行故障排除

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>列出状态监视器 v2 模块中的可用命令
运行 `Get-Command -Module Az.ApplicationMonitor` 命令来获取可用命令：

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>确定状态监视器 v2 模块的当前版本
运行 `Get-ApplicationInsightsMonitoringStatus` 命令来显示有关该模块的以下信息：
   - PowerShell 模块版本
   - Application Insights SDK 版本
   - PowerShell 模块的文件路径
    
有关如何使用此 cmdlet 的详细说明，请查看 [API 参考](status-monitor-v2-api-get-status.md)。


### <a name="troubleshooting-running-processes"></a>对正在运行的进程进行故障排除

可以检查已检测的计算机上的进程，以确定是否加载了所有 DLL。
如果监视功能在正常工作，则至少有 12 个 DLL 应加载。

使用 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` 命令来检查 DLL。

有关如何使用此 cmdlet 的详细说明，请查看 [API 参考](status-monitor-v2-api-get-status.md)。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 日志

#### <a name="setup"></a>安装

1. 从 [GitHub](https://github.com/Microsoft/perfview/releases) 下载 PerfView.exe 和 PerfView64.exe。
2. 启动 PerfView64.exe。
3. 展开“高级选项”  。
4. 清除这些复选框：
    - **Zip**
    - **合并**
    - **.NET 符号集合**
5. 设置以下“其他提供程序”  ：`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集日志

1. 在具有管理员权限的命令控制台中，运行 `iisreset /stop` 命令以禁用 IIS 和所有 Web 应用。
2. 在 PerfView 中，选择“开始收集”  。
3. 在具有管理员权限的命令控制台中，运行 `iisreset /start` 命令以启动 IIS。
4. 尝试浏览到你的应用。
5. 在应用加载后，请返回到 PerfView，然后选择“停止收集”  。



## <a name="next-steps"></a>后续步骤

- 请查看 [API 参考](status-monitor-v2-overview.md#powershell-api-reference)，以了解你可能错过的参数。
- 如果遇到此处未列出的问题，可以通过 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) 联系我们。
