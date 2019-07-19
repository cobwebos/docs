---
title: Azure 状态监视器 v2 详细说明 |Microsoft Docs
description: 有关如何开始使用状态监视器 v2 的详细说明。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
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
ms.openlocfilehash: 02f4fa45cbfa619825478520961b6411459973e4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326280"
---
# <a name="status-monitor-v2-detailed-instructions"></a>状态监视器 v2：详细说明

本文介绍如何载入到 PowerShell 库和下载 ApplicationMonitor 模块。
所含的最常见参数是你需要入门的最常见参数。
我们还提供了手动下载说明, 以防你无法访问 internet。

## <a name="get-an-instrumentation-key"></a>获取检测密钥

若要开始，需要一个检测密钥。 有关详细信息，请参阅[创建 Application Insights 资源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>以管理员身份使用提升的执行策略运行 PowerShell

### <a name="run-as-admin"></a>以管理员身份运行

PowerShell 需要拥有管理员级别的权限才能对计算机进行更改。
### <a name="execution-policy"></a>执行策略
- 说明:默认禁用 PowerShell 脚本的运行。 我们建议仅允许在当前范围运行 RemoteSigned 脚本。
- 参考：[关于执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)和 [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 命令：`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 可选参数：
    - `-Force`。 跳过确认提示。

**示例错误**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

运行 `$PSVersionTable` 命令，审核 PowerShell 的实例。
该命令生成以下输出：


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

这些说明是在运行 Windows 10 的计算机上编写并测试的，使用了上面所列的版本。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 库的先决条件

这些步骤会准备你的服务器，以从 PowerShell 库下载模块。

> [!NOTE] 
> Windows 10、Windows Server 2016 和 PowerShell 6 支持 PowerShell 库。
> 有关早期版本的信息，请参阅[安装 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。


1. 以管理员身份使用提升的执行策略运行 PowerShell。
2. 安装 NuGet 包提供程序。
    - 说明:需要使用此提供程序才能与基于 NuGet 的存储库（例如 PowerShellGallery）交互。
    - 参考：[Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - 命令：`Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-Force`。 跳过确认提示。
    
    如果未设置 NuGet，则会看到此提示：
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 将 PowerShell 库配置为受信任的存储库。
    - 说明:默认情况下，PowerShell 库是不受信任的存储库。
    - 参考：[Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 命令：`Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。

    如果 PowerShell 库不受信任，则会看到此提示：

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    可以运行 `Get-PSRepository` 命令，确认此更改并审核所有 PowerShell 存储库。

4. 安装最新版本的 PowerShellGet。
    - 说明:此模块包含用于从 PowerShell 库中获取其他模块的工具。 Windows 10 和 Windows Server 随附了版本 1.0.0.1。 需要 1.6.0 或更高版本。 若要确定安装了哪个版本，请运行 `Get-Command -Module PowerShellGet` 命令。
    - 参考：[安装 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。
    - 命令：`Install-Module -Name PowerShellGet`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-Force`。 忽略“已安装”警告并安装最新版本。

    如果使用的不是最新 PowerShellGet 版本，则会看到此错误：
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重启 PowerShell。 无法在当前会话中加载新版本。 新 PowerShell 会话会加载最新版 PowerShellGet。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>通过 PowerShell 库下载并安装模块

这些步骤将从 PowerShell 库下载 Az.ApplicationMonitor 模块。

1. 确保满足 PowerShell 库的所有先决条件。
2. 以管理员身份使用提升的执行策略运行 PowerShell。
3. 安装 Az.ApplicationMonitor 模块。
    - 参考：[Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - 命令：`Install-Module -Name Az.ApplicationMonitor`。
    - 可选参数：
        - `-Proxy`。 指定请求的代理服务器。
        - `-AllowPrerelease`。 允许安装 alpha 和 beta 版本。
        - `-AcceptLicense`。 跳过“接受许可证”提示
        - `-Force`。 忽略“不受信任的存储库”警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>手动下载并安装模块（脱机选项）

如果出于任何原因无法连接到 PowerShell 模块，可以手动下载并安装 Az.ApplicationMonitor 模块。

### <a name="manually-download-the-latest-nupkg-file"></a>手动下载最新的 nupkg 文件

1. 转到  https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。
2. 在“版本历史记录”表中选择文件的最新版本。 
3. 在“安装选项”下选择“手动下载”。  

### <a name="option-1-install-into-a-powershell-modules-directory"></a>选项 1：安装到 PowerShell 模块目录中
将手动下载的 PowerShell 模块安装到 PowerShell 目录中，使之可被 PowerShell 会话发现。
有关详细信息，请参阅[安装 PowerShell 模块](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>使用 Expand-Archive (v1.0.1.0) 将 nupkg 作为 zip 文件解压缩

- 说明:基础版本的 Microsoft.PowerShell.Archive (v1.0.1.0) 无法解压缩 nupkg 文件。 使用 .zip 扩展名重命名文件。
- 参考：[Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>使用 Expand-Archive (v1.1.0.0) 解压缩 nupkg

- 说明:使用最新版本的 Expand-Archive 解压缩 nupkg 文件但不更改扩展名。
- 参考：[Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) 和 [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>选项 2：手动解压缩并导入 nupkg
将手动下载的 PowerShell 模块安装到 PowerShell 目录中，使之可被 PowerShell 会话发现。
有关详细信息，请参阅[安装 PowerShell 模块](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。

若要将模块安装到任何其他目录中，请使用 [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6) 手动导入模块。

> [!IMPORTANT] 
> DLL 会通过相对路径进行安装。
> 请将此包的内容存储到所需的运行时目录中，并确认访问权限允许读取但不允许写入。

1. 将扩展名更改为“.zip”，并将包的内容解压缩到所需的安装目录中。
2. 找到 Az.ApplicationMonitor.psd1 的文件路径。
3. 以管理员身份使用提升的执行策略运行 PowerShell。
4. 通过 `Import-Module Az.ApplicationMonitor.psd1` 命令加载模块。
    

## <a name="route-traffic-through-a-proxy"></a>通过代理路由流量

监视专用 Intranet 中的计算机时，需要通过代理路由 HTTP 流量。

用于从 PowerShell 库下载并安装 Az.ApplicationMonitor 的 PowerShell 命令支持 `-Proxy` 参数。
编写安装脚本时，请查看上述说明。

Application Insights SDK 需要将应用的遥测数据发送给 Microsoft。 建议在 web.config 文件中配置应用的代理设置。 有关详细信息，请参阅 [Application Insights 常见问题解答：代理透传](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>启用监视

使用 `Enable-ApplicationInsightsMonitoring` 命令以启用监视。

有关如何使用此 cmdlet 的详细说明，请参阅 [API 参考](status-monitor-v2-api-enable-monitoring.md)。



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

- 使用我们的指南对状态监视器 v2 进行[故障排除](status-monitor-v2-troubleshoot.md)。
