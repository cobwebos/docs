---
title: Azure 应用程序 Insights 代理详细说明 |Microsoft Docs
description: Application Insights 代理入门的详细说明。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cd5ca5039b537859d5b31c901ed1f93877ecb629
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374882"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights 代理（以前称为状态监视器 v2）：详细说明

本文介绍如何载入 PowerShell 库并下载 ApplicationMonitor 模块。
所含的最常见参数是你需要入门的最常见参数。
我们还提供了手动下载说明，以防你无法访问 internet。

## <a name="get-an-instrumentation-key"></a>获取检测密钥

若要开始，需要一个检测密钥。 有关详细信息，请参阅[创建 Application Insights 资源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>使用提升的执行策略以管理员身份运行 PowerShell

### <a name="run-as-admin"></a>以管理员身份运行

PowerShell 需要管理员级权限才能对你的计算机进行更改。
### <a name="execution-policy"></a>执行策略
- 说明：默认情况下，禁用正在运行的 PowerShell 脚本。 建议只为当前作用域允许 RemoteSigned 脚本。
- 参考：[关于执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)和[set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 命令： `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 可选参数：
    - `-Force` 列中的一个值匹配。 跳过确认提示。

**示例错误**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 先决条件

通过运行 `$PSVersionTable` 命令审核 PowerShell 的实例。
此命令生成以下输出：


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

这些说明是在运行 Windows 10 的计算机上编写和测试的，以及上面列出的版本。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 库的先决条件

这些步骤将使你的服务器能够从 PowerShell 库下载模块。

> [!NOTE] 
> Windows 10、Windows Server 2016 和 PowerShell 6 支持 PowerShell 库。
> 有关更早版本的信息，请参阅[安装 PowerShellGet](/powershell/scripting/gallery/installing-psget)。


1. 使用提升的执行策略以管理员身份运行 PowerShell。
2. 安装 NuGet 程序包提供程序。
    - 说明：需要此访问接口才能与基于 NuGet 的存储库（如 PowerShell 库）进行交互。
    - 参考： [install-packageprovider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - 命令： `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 可选参数：
        - `-Proxy` 列中的一个值匹配。 为请求指定代理服务器。
        - `-Force` 列中的一个值匹配。 跳过确认提示。
    
    如果未设置 NuGet，则会收到以下提示：
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 将 PowerShell 库配置为受信任的存储库。
    - 说明：默认情况下，PowerShell 库是不受信任的存储库。
    - Reference： [register-psrepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 命令： `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 可选参数：
        - `-Proxy` 列中的一个值匹配。 为请求指定代理服务器。

    如果 PowerShell 库不受信任，你将收到此提示：

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    可以通过运行 `Get-PSRepository` 命令确认此更改并审核所有 PSRepositories。

4. 安装最新版本的 PowerShellGet。
    - 说明：此模块包含用于从 PowerShell 库获取其他模块的工具。 版本1.0.0.1 随 Windows 10 和 Windows Server 一起提供。 需要1.6.0 或更高版本。 若要确定安装的版本，请运行 `Get-Command -Module PowerShellGet` 命令。
    - 参考：[安装 PowerShellGet](/powershell/scripting/gallery/installing-psget)。
    - 命令： `Install-Module -Name PowerShellGet`。
    - 可选参数：
        - `-Proxy` 列中的一个值匹配。 为请求指定代理服务器。
        - `-Force` 列中的一个值匹配。 跳过 "已安装" 警告并安装最新版本。

    如果使用的是最新版本的 PowerShellGet，会收到此错误：
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重新启动 PowerShell。 无法在当前会话中加载新版本。 新的 PowerShell 会话将加载 PowerShellGet 的最新版本。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>通过 PowerShell 库下载并安装模块

这些步骤将从 PowerShell 库下载 ApplicationMonitor 模块。

1. 确保满足 PowerShell 库的所有先决条件。
2. 使用提升的执行策略以管理员身份运行 PowerShell。
3. 安装 ApplicationMonitor 模块。
    - 参考：[安装模块](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - 命令： `Install-Module -Name Az.ApplicationMonitor`。
    - 可选参数：
        - `-Proxy` 列中的一个值匹配。 为请求指定代理服务器。
        - `-AllowPrerelease` 列中的一个值匹配。 允许安装 alpha 和 beta 版本。
        - `-AcceptLicense` 列中的一个值匹配。 绕过 "接受许可证" 提示
        - `-Force` 列中的一个值匹配。 跳过 "不受信任的存储库" 警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>手动下载并安装该模块（脱机选项）

如果出于任何原因无法连接到 PowerShell 模块，可以手动下载并安装 ApplicationMonitor 模块。

### <a name="manually-download-the-latest-nupkg-file"></a>手动下载最新的 nupkg 文件

1. 转到  https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。
2. 选择**版本历史记录**表中的最新版本的文件。
3. 在 "**安装选项**" 下，选择 "**手动下载**"。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>选项1：安装到 PowerShell 模块目录
将手动下载的 PowerShell 模块安装到 PowerShell 目录中，以便 PowerShell 会话可以发现它。
有关详细信息，请参阅[安装 PowerShell 模块](/powershell/scripting/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>使用展开存档将 nupkg 解压缩为 zip 文件（v 1.0.1.0）

- 说明：1.0.1.0 的基本版本（v）无法解压缩 nupkg 文件。 用 .zip 扩展名重命名该文件。
- 参考：[展开-存档](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>使用展开-存档解压缩 nupkg （v 1.1.0.0）

- 说明：使用当前版本的扩展-存档来解压缩 nupkg 文件，而无需更改扩展。
- 参考：[展开-存档](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)和[Microsoft PowerShell。](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>选项2：手动解压缩和导入 nupkg
将手动下载的 PowerShell 模块安装到 PowerShell 目录中，以便 PowerShell 会话可以发现它。
有关详细信息，请参阅[安装 PowerShell 模块](/powershell/scripting/developer/module/installing-a-powershell-module)。

如果要将模块安装到任何其他目录中，请使用[import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)手动导入该模块。

> [!IMPORTANT] 
> 将通过相对路径安装 Dll。
> 将包的内容存储在所需的运行时目录中，并确认访问权限允许读取但不允许写入。

1. 将扩展名更改为 ".zip"，并将包的内容提取到预期的安装目录中。
2. 查找 ApplicationMonitor. psd1 的文件路径。
3. 使用提升的执行策略以管理员身份运行 PowerShell。
4. 使用 `Import-Module Az.ApplicationMonitor.psd1` 命令加载模块。
    

## <a name="route-traffic-through-a-proxy"></a>通过代理路由流量

监视专用 intranet 上的计算机时，需要通过代理路由 HTTP 流量。

要从 PowerShell 库中下载和安装 ApplicationMonitor 的 PowerShell 命令支持 `-Proxy` 参数。
编写安装脚本时，请查看前面的说明。

Application Insights SDK 需要将你的应用的遥测数据发送给 Microsoft。 建议在 web.config 文件中为应用配置代理设置。 有关详细信息，请参阅[APPLICATION INSIGHTS FAQ： Proxy passthrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>启用监视

使用 `Enable-ApplicationInsightsMonitoring` 命令启用监视。

请参阅[API 参考](status-monitor-v2-api-enable-monitoring.md)，了解有关如何使用此 cmdlet 的详细说明。



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

- 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
