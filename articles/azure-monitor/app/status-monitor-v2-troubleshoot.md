---
title: Azure 应用程序 Insights 代理疑难解答和已知问题 |Microsoft Docs
description: Application Insights 代理和故障排除示例的已知问题。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 217629ba5c386557455cc2d2b8bd47f85fa8f84e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671148"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights 代理（以前称为状态监视器 v2）的疑难解答

启用监视后，可能会遇到阻止数据收集的问题。
本文列出了所有已知问题，并提供了故障排除示例。
如果遇到此处未列出的问题，可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上联系我们。

## <a name="known-issues"></a>已知问题

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>应用的 bin 目录中的 Dll 冲突

如果这些 Dll 中有任何一个存在于 bin 目录中，则监视可能会失败：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

其中一些 Dll 包含在 Visual Studio 默认应用程序模板中，即使你的应用程序不使用它们也是如此。
你可以使用故障排除工具来查看症状行为：

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和应用加载（不含遥测）。 调查 Sysinternals （ListDLLs）：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>与 IIS 共享配置冲突

如果你有 web 服务器的群集，则可能使用的是[共享配置](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 无法注入此共享配置。
在每个 web 服务器上运行 "启用" 命令，将 DLL 安装到每个服务器的 GAC 中。

运行 Enable 命令后，请完成以下步骤：
1. 中转到共享配置目录并找到 Applicationhost.config 文件。
2. 将以下行添加到配置的模块部分：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 嵌套应用程序

我们不会在1.0 版的 IIS 中检测嵌套的应用程序。
我们将在[此处](https://github.com/microsoft/ApplicationInsights-Home/issues/369)跟踪此问题。

### <a name="advanced-sdk-configuration-isnt-available"></a>高级 SDK 配置不可用。

SDK 配置未向最终用户公开，版本为1.0。
我们将在[此处](https://github.com/microsoft/ApplicationInsights-Home/issues/375)跟踪此问题。

    
    
## <a name="troubleshooting"></a>故障排除
    
### <a name="troubleshooting-powershell"></a>PowerShell 疑难解答

#### <a name="determine-which-modules-are-available"></a>确定可用的模块
您可以使用 `Get-Module -ListAvailable` 命令来确定安装了哪些模块。

#### <a name="import-a-module-into-the-current-session"></a>将模块导入到当前会话中
如果某个模块尚未加载到 PowerShell 会话中，则可以使用 `Import-Module <path to psd1>` 命令手动加载该模块。


### <a name="troubleshooting-the-application-insights-agent-module"></a>Application Insights 代理模块疑难解答

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>列出 Application Insights 代理模块中可用的命令
运行命令 `Get-Command -Module Az.ApplicationMonitor` 以获取可用命令：

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>确定 Application Insights 代理模块的当前版本
运行 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 命令以显示有关该模块的以下信息：
   - PowerShell 模块版本
   - Application Insights SDK 版本
   - PowerShell 模块的文件路径
    
查看[API 参考](status-monitor-v2-api-get-status.md)，了解有关如何使用此 cmdlet 的详细说明。


### <a name="troubleshooting-running-processes"></a>运行进程疑难解答

您可以检查已检测计算机上的进程，以确定是否加载了所有 Dll。
如果监视正常工作，则应加载至少12个 Dll。

使用 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` 命令来检查 Dll。

查看[API 参考](status-monitor-v2-api-get-status.md)，了解有关如何使用此 cmdlet 的详细说明。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 收集 ETW 日志

#### <a name="setup"></a>设置

1. 从[GitHub](https://github.com/Microsoft/perfview/releases)下载 PerfView 和 PerfView64。
2. 启动 PerfView64。
3. 展开“高级选项”。
4. 清除以下复选框：
    - **Zip**
    - **合并**
    - **.NET 符号集合**
5. 设置以下**其他提供程序**： `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集日志

1. 在具有管理员权限的命令控制台中，运行 `iisreset /stop` 命令以关闭 IIS 和所有 web 应用。
2. 在 PerfView 中，选择 "**开始收集**"。
3. 在具有管理员权限的命令控制台中，运行 `iisreset /start` 命令以启动 IIS。
4. 尝试浏览到您的应用程序。
5. 加载应用后，返回到 PerfView 并选择 "**停止收集**"。



## <a name="next-steps"></a>后续步骤

- 查看[API 参考](status-monitor-v2-overview.md#powershell-api-reference)，了解可能缺少的参数。
- 如果遇到此处未列出的问题，可以在[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)上联系我们。
