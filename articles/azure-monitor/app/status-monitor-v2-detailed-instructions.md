---
title: Azure 状态监视器 v2 的详细说明 |Microsoft Docs
description: 有关如何开始使用状态监视器 v2 的详细说明。 无需重新部署网站即可监视网站性能。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
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
ms.openlocfilehash: c8199c960229f9cc53cf57f9da3e1f17ebd9f5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074163"
---
# <a name="status-monitor-v2-detailed-instructions"></a>状态监视器 v2:详细说明

本文介绍如何登记到 PowerShell 库和下载 ApplicationMonitor 模块。
它描述你将需要开始的最常见参数。
在没有 internet 访问权限的情况下，它还包括手动说明。

> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="get-an-instrumentation-key"></a>获取检测密钥

若要开始，你需要检测密钥。 有关详细信息，请参阅[创建 Application Insights 资源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>使用提升权限的执行策略以管理员身份运行 PowerShell

**以管理员身份运行**

PowerShell 需要管理员级别权限，无法对您的计算机进行更改。

**执行策略**
- 说明:默认情况下，禁用运行 PowerShell 脚本。 我们建议先让 RemoteSigned 脚本仅当前作用域。
- 参考：[有关执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)并[Set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 命令： `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 可选参数：
    - `-Force`。 绕过确认提示。

**示例错误**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

通过运行审核你的 PowerShell 实例`$PSVersionTable`命令。
此命令将生成以下输出：


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

这些说明进行编写和运行 Windows 10 和上面列出的版本的计算机上测试。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 库的先决条件

这些步骤将准备你的服务器来从 PowerShell 库下载模块。

> [!NOTE] 
> PowerShell 库支持 Windows 10、 Windows Server 2016 和 PowerShell 6。
> 有关早期版本的信息，请参阅[安装 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。


1. 使用提升权限的执行策略，以管理员身份运行 PowerShell。
2. 安装 NuGet 包提供程序。
    - 说明:您需要此提供程序与基于 NuGet 的 PowerShell 库这样的存储库进行交互。
    - 参考：[Install-packageprovider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - 命令： `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-Force`。 绕过确认提示。
    
    如果未设置 NuGet，则会看到此提示：
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 将 PowerShell 库配置为受信任的存储库。
    - 说明:默认情况下，PowerShell 库是一个不受信任的存储库。
    - 参考：[Set-psrepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 命令： `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。

    如果 PowerShell 库不受信任，则会看到此提示：

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    可以确认此更改，并通过运行审核所有 PSRepositories`Get-PSRepository`命令。

4. 安装最新版本的 PowerShellGet。
    - 说明:此模块包含用于从 PowerShell 库中获取其他模块的工具。 版本 1.0.0.1 随附于 Windows 10 和 Windows Server。 版本 1.6.0 或更高版本是必需的。 若要确定安装哪个版本，请运行`Get-Command -Module PowerShellGet`命令。
    - 参考：[安装 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。
    - 命令： `Install-Module -Name PowerShellGet`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-Force`。 绕过"已安装"警告并安装最新版本。

    如果不使用最新版本的 PowerShellGet，你会收到此错误：
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重启 PowerShell。 无法在当前会话中加载新版本。 新的 PowerShell 会话将加载 PowerShellGet 的最新版本。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>下载并安装通过 PowerShell 库模块

这些步骤将从 PowerShell 库下载 Az.ApplicationMonitor 模块。

1. 请确保满足所有先决条件 PowerShell 库。
2. 使用提升权限的执行策略，以管理员身份运行 PowerShell。
3. 安装 Az.ApplicationMonitor 模块。
    - 参考：[安装模块](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - 命令： `Install-Module -Name Az.ApplicationMonitor`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-AllowPrerelease`。 允许的 alpha 和 beta 版本的安装。
        - `-AcceptLicense`。 绕过"接受许可证"提示符
        - `-Force`。 绕过"不受信任存储库"警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>下载并手动安装的模块 （offline 选项）

如果出于任何原因无法连接到 PowerShell 模块，你可以手动下载并安装 Az.ApplicationMonitor 模块。

### <a name="manually-download-the-latest-nupkg-file"></a>手动下载最新的 nupkg 文件

1. 转到  https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。
2. 选择中的文件的最新版本**版本历史记录**表。
3. 下**安装选项**，选择**手动下载**。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>选项 1：安装 PowerShell 模块目录到
手动下载的 PowerShell 模块安装到 PowerShell 目录中，使其可发现 PowerShell 会话。
有关详细信息，请参阅[安装 PowerShell 模块](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>通过使用 Expand-archive (v1.0.1.0) 为 zip 文件解压缩 nupkg

- 说明:基础版本的 Microsoft.PowerShell.Archive (v1.0.1.0) 无法解压缩 nupkg 文件。 重命名该文件具有.zip 扩展名。
- 参考：[展开存档](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>通过使用 Expand-archive (v1.1.0.0) 解压缩 nupkg

- 说明:使用 Expand-archive 了最新版本将 nupkg 文件解压缩而无需更改该扩展。
- 参考：[展开存档](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)并[Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>选项 2：解压缩，然后手动导入 nupkg
手动下载的 PowerShell 模块安装到 PowerShell 目录中，使其可发现 PowerShell 会话。
有关详细信息，请参阅[安装 PowerShell 模块](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。

如果要在模块安装到任何其他目录，手动导入模块通过使用[导入模块](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)。

> [!IMPORTANT] 
> 通过相对路径将安装的 Dll。
> 在你预期的运行时的目录中存储包的内容并确认访问权限，允许读取但不是能写入。

1. 将扩展名更改为".zip"并将包的内容提取到你的预期的安装目录。
2. 查找 Az.ApplicationMonitor.psd1 的文件路径。
3. 使用提升权限的执行策略，以管理员身份运行 PowerShell。
4. 加载模块，通过使用`Import-Module Az.ApplicationMonitor.psd1`命令。
    

## <a name="route-traffic-through-a-proxy"></a>通过代理路由流量

如果您的专用 intranet 上监视一台计算机，将需要将 HTTP 流量通过代理路由。

PowerShell 命令，以下载并安装 Az.ApplicationMonitor 从 PowerShell 库支持`-Proxy`参数。
在编写安装脚本时，请查看前面的说明。

Application Insights SDK 将需要将应用的遥测数据发送给 Microsoft。 我们建议在 web.config 文件中，为您的应用程序配置代理设置。 有关详细信息，请参阅[Application Insights 常见问题：代理传递](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>启用监视

使用`Enable-ApplicationInsightsMonitoring`命令以启用监视。

请参阅[API 参考](status-monitor-v2-api-enable-monitoring.md)有关如何使用此 cmdlet 的详细说明。



## <a name="next-steps"></a>后续步骤

 查看遥测：

- [浏览指标](../../azure-monitor/app/metrics-explorer.md)监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)来诊断问题。
- [使用 Analytics](../../azure-monitor/app/analytics.md)获取更多高级查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

 添加更多遥测：

- [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常，并启用跟踪调用。
- [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和记录调用。

使用状态监视器 v2 执行更多操作：

- 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
