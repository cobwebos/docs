---
title: 安装适用于 Azure Stack 的 PowerShell | Microsoft Docs
description: 了解如何安装适用于 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: fe64011991732c7493d8efd06516efc664b674a4
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752769"
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

要使用云，必须安装与 Azure Stack 兼容的 PowerShell 模块。 可通过名为“API 配置文件”的功能来实现兼容性。

API 配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持特定日期的配置文件版本等**2018年-03-01-混合**。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可在已连接到 Internet、部分联网或离线场景中安装与 Azure Stack 兼容的 PowerShell 模块。 本文分步详细介绍了如何针对这些场景安装适用于 Azure Stack 的 PowerShell。

## <a name="1-verify-your-prerequisites"></a>1.验证先决条件

开始使用 Azure Stack 和 PowerShell 之前，必须具备以下先决条件：

- **PowerShell 版本 5.0** 若要检查版本，请运行 **$PSVersionTable.PSVersion** 并比较**主**版本。 如果没有 PowerShell 5.0，请根据[安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 中所述进行操作。

  > [!Note]
  > PowerShell 5.0 需要 Windows 计算机。

- **在提升的命令提示符中运行 Powershell**。
  必须以管理权限运行 PowerShell。

- **PowerShell 库访问权限** 需要访问 [PowerShell 库](https://www.powershellgallery.com)。 该库是 PowerShell 内容的中心存储库。 **PowerShellGet** 模块包含用于发现、安装、更新和发布 PowerShell 项目（例如来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能与脚本）的 cmdlet。 如果在离线场景中使用 PowerShell，则必须从已建立 Internet 连接的计算机检索资源，并将其存储在离线计算机可访问的位置。


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.验证 PowerShell 库的可访问性

验证 PSGallery 是否已注册为存储库。

> [!Note]
> 此步骤需要访问 Internet。

打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

如果未注册存储库，请打开提升的 PowerShell 会话并运行以下命令：

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3.卸载 Azure Stack PowerShell 模块的现有版本

在安装所需版本之前，请确保卸载以前安装的任何 Azure Stack AzureRM PowerShell 模块。 可使用以下两种方法之一卸载现有版本：

1. 若要卸载现有的 AzureRM PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    如果遇到错误如模块已在使用，请关闭正在使用的模块，然后重新运行上述脚本的 PowerShell 会话。

2. 删除开头的所有文件夹`Azure`或`Azs.`从`C:\Program Files\WindowsPowerShell\Modules`和`C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.已联网：在已建立 Internet 连接的情况下安装适用于 Azure Stack 的 PowerShell

对于 Azure Stack 版本 1808，Azure Stack 需要 **2018-03-01-hybrid** API 版本。 可以通过安装 **AzureRM.Bootstrapper** 模块获得该配置文件。 除了 AzureRM 模块以外，还应安装 Azure Stack 特定的 Azure PowerShell 模块。 你需要的 API 版本配置文件和 Azure Stack PowerShell 模块将取决于你运行的 Azure Stack 版本。

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

- Azure Stack 1811 或更高版本。

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

    要使用其他存储功能 （在连接部分中所述），下载并安装以下包。

    ```PowerShell
    # Install the Azure.Storage module version 4.5.0
    Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

    # Install the AzureRm.Storage module version 5.0.4
    Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

    # Remove incompatible storage module installed by AzureRM.Storage
    Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

    # Load the modules explicitly specifying the versions
    Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
    Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
    ```

> [!Note]
> 若要将 Azure PowerShell 从 **2017-03-09-profile** 升级到 **2018-03-01-hybrid**，请参阅[迁移指南](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md)。


- Azure Stack 1808 或更高版本。

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

- Azure Stack 1807 或更早版本。

  ```PowerShell
  Install-Module -Name AzureRm.BootStrapper
  Use-AzureRmProfile -Profile 2017-03-09-profile -Force
  Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

运行以下命令来确认安装：

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.离线：在未建立 Internet 连接的情况下安装 PowerShell

在离线场景中，必须先将 PowerShell 模块下载到已建立 Internet 连接的计算机，然后将其传送到 Azure Stack 开发工具包进行安装。

登录到已建立 Internet 连接的计算机，并根据 Azure Stack 的版本，使用以下脚本下载 Azure 资源管理器和 AzureStack 程序包：

  - Azure Stack 1811 或更高版本。

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

    要使用其他存储功能 （在连接部分中所述），下载并安装以下包。

    ```PowerShell
    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
    ```

  - Azure Stack 1808 或更高版本。

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

  - Azure Stack 1807 或更早版本。

    > [!Note]
    若要从 1.2.11 版升级，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ```

2. 将下载的程序包复制到 USB 设备。

3. 登录到工作站，将包从 USB 设备复制到工作站中的某个位置。

4. 现在，将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.配置 PowerShell 以使用代理服务器

在需要代理服务器访问 Internet 的场景中，必须先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升的 PowerShell 命令提示符。
2. 运行以下命令：

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>后续步骤

 - [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
 - [配置 Azure Stack 用户的 PowerShell 环境](user/azure-stack-powershell-configure-user.md)
 - [配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
 - [在 Azure Stack 中管理 API 版本配置文件](user/azure-stack-version-profiles.md)