---
title: Azure 状态监视器 v2-入门 |Microsoft Docs
description: 状态监视器 v2 的快速入门指南。 监视网站性能，无需重新部署该网站。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
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
ms.openlocfilehash: 4da9d8e8efd5f70718f18b2e8e35ea6b5adf6757
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734981"
---
# <a name="get-started-with-status-monitor-v2"></a>开始使用状态监视器 v2

本文包含应适用于大多数环境的快速启动命令。
说明取决于 PowerShell 库分发更新。
这些命令支持 PowerShell`-Proxy`参数。

有关这些命令、 自定义项的说明，以及有关故障排除信息的说明，请参阅[详细说明](status-monitor-v2-detailed-instructions.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 状态监视器 v2 目前处于公共预览状态。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="download-and-install-via-powershell-gallery"></a>下载并安装通过 PowerShell 库

### <a name="install-prerequisites"></a>安装必备组件
管理员身份运行 PowerShell
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
关闭 PowerShell。

### <a name="install-status-monitor-v2"></a>安装状态监视 v2
管理员身份运行 PowerShell
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>启用监视
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手动下载并安装 （offline 选项）
### <a name="download-the-module"></a>下载该模块
手动下载最新版本中的模块[PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)。

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

- [浏览指标](../../azure-monitor/app/metrics-explorer.md)监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)来诊断问题。
- [使用 Analytics](../../azure-monitor/app/analytics.md)获取更多高级查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

 添加更多遥测：

- [创建 web 测试](monitor-web-app-availability.md)以确保你的站点保持活动状态。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常，并启用跟踪调用。
- [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和记录调用。

用做更多状态监视器 v2:

- 审阅[详细说明](status-monitor-v2-detailed-instructions.md)有关以下网页中找到的命令的说明。
- 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
