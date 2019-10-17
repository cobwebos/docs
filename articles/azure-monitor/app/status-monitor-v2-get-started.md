---
title: Azure 应用程序 Insights 代理-入门 |Microsoft Docs
description: Application Insights 代理的快速入门指南。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
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
ms.openlocfilehash: f1911d8187b186f301bea771963f922ee3574fd6
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388212"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>开始 Azure Monitor 本地服务器 Application Insights 代理

本文包含了预计适用于大多数环境的快速入门命令。
说明取决于分发更新的 PowerShell 库。
这些命令支持 PowerShell `-Proxy` 参数。

有关这些命令、自定义说明以及有关故障排除的信息的说明，请参阅[详细说明](status-monitor-v2-detailed-instructions.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>通过 PowerShell 库下载和安装

### <a name="install-prerequisites"></a>安装必备组件
以管理员身份运行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
关闭 PowerShell。

### <a name="install-application-insights-agent"></a>安装 Application Insights 代理
以管理员身份运行 PowerShell。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>启用监视
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手动下载并安装（脱机选项）
### <a name="download-the-module"></a>下载模块
手动从[PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)下载模块的最新版本。

### <a name="unzip-and-install-application-insights-agent"></a>解压缩并安装 Application Insights 代理
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>启用监视
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

- 查看[详细说明，详细](status-monitor-v2-detailed-instructions.md)了解此处提供的命令。
- 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
