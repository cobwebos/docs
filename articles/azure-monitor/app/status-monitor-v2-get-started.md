---
title: Azure 状态监视器 v2-入门 |Microsoft Docs
description: 状态监视器 v2 的快速入门指南。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
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
ms.openlocfilehash: d9c354edac3cbd3faccaa261654e56f858befdf6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058234"
---
# <a name="get-started-with-status-monitor-v2"></a>状态监视器 v2 入门

本文包含应适用于大多数环境的快速入门命令。
这些说明依赖于 PowerShell 库来分发更新。
这些命令支持 PowerShell `-Proxy` 参数。

有关这些命令的说明、自定义说明以及有关故障排除的信息，请参阅[详细说明](status-monitor-v2-detailed-instructions.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-and-install-via-powershell-gallery"></a>通过 PowerShell 库下载并安装

### <a name="install-prerequisites"></a>安装先决条件
以管理员身份运行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
关闭 PowerShell。

### <a name="install-status-monitor-v2"></a>安装状态监视器 v2
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
从 [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)手动下载最新版本的模块。

### <a name="unzip-and-install-status-monitor-v2"></a>解压缩并安装状态监视器 v2
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

- [浏览指标](../../azure-monitor/app/metrics-explorer.md)，以便监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- [使用分析](../../azure-monitor/app/analytics.md)，以便进行更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

 添加更多遥测：

- [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用。

使用状态监视器 v2 执行更多操作：

- 有关此处找到的命令的说明，请查看[详细说明](status-monitor-v2-detailed-instructions.md)。
- 使用我们的指南可[排查状态监视器 v2 问题](status-monitor-v2-troubleshoot.md)。
