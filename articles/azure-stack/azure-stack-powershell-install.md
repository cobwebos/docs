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
ms.date: 09/17/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 688e9f7d23fd79c42e04be59419f6fa93c949aea
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984452"
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

若要使用你的云，必须安装 Azure Stack 兼容的 PowerShell 模块。 可通过名为“API 配置文件”的功能来实现兼容性。

API 配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持特定日期的配置文件版本等**2018年-03-01-混合**，而 Azure 则支持**最新**API 版本配置文件。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。  

你可以安装 Azure Stack 兼容的 PowerShell 模块中 Internet 连接、 部分联网或断开连接的方案。 本文将指导完成安装对于这种情况适用于 Azure Stack PowerShell 的详细说明。

## <a name="1-verify-your-prerequisites"></a>1.验证先决条件

开始使用 Azure Stack 和 PowerShell 之前，必须具备以下先决条件：

- **PowerShell 版本 5.0**  
若要检查版本，请运行 **$PSVersionTable.PSVersion**并将进行比较**主要**版本。 如果不 PowerShell 5.0，请遵循[安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)。

  > [!Note]  
  > PowerShell 5.0 需要 Windows 计算机。

- **在提升的命令提示符中运行 Powershell**  
  必须使用管理权限运行 PowerShell。

- **PowerShell 库访问权限**  
  需有权[PowerShell 库](https://www.powershellgallery.com)。 该库是 PowerShell 内容的中心存储库。 **PowerShellGet** 模块包含用于发现、安装、更新和发布 PowerShell 项目（例如来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能与脚本）的 cmdlet。 如果在离线场景中使用 PowerShell，必须使用连接计算机中检索资源，到 Internet，并将其存储在断开连接的计算机可以访问的位置。


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.验证 PowerShell 库可访问性

验证 PSGallery 是否已注册为存储库。

> [!Note]  
> 此步骤需要访问 Internet。 

打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

如果未注册存储库，请打开提升的 PowerShell 会话并运行以下命令：

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3.卸载 Azure Stack PowerShell 模块的现有版本

在安装所需版本之前，请确保卸载以前安装的任何 Azure Stack AzureRM PowerShell 模块。 可使用以下两种方法之一卸载现有版本：

1. 若要卸载现有的 AzureRM PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
    Get-Module Azs.* -ListAvailable | Uninstall-Module -Force
  ````

2. 从 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 文件夹中删除以 `Azure` 开头的所有文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.联网：在已建立 Internet 连接的情况下安装适用于 Azure Stack 的 PowerShell

Azure Stack 需要**2018年-03-01-混合**1808年的 Azure Stack 版本的 API 版本配置文件。 配置文件可通过安装**AzureRM.Bootstrapper**模块。 此外，与 AzureRM 模块，你还应安装 Azure Stack 特定 PowerShell 模块。 API 版本配置文件和所需的 Azure Stack PowerShell 模块将取决于版本的 Azure Stack 系统运行。

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

  - Azure Stack 1808 或更高版本。

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0 -Force
    ```

> [!Note]  
> 若要升级 Azure PowerShell 从**2017年-03-09-profile**到**2018年-03-01-混合**，请参阅[迁移指南](https://github.com/bganapa/azure-powershell/blob/migration-guide/documentation/migration-guides/Stack/migration-guide.2.3.0.md)。

  - Azure Stack 1807 或更早版本。

    ```PowerShell  
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0 -Force
    ```

  - 1804 或更早版本的 azure Stack。

    ```PowerShell  
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force
    ```

运行以下命令来确认安装：

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.离线：在未建立 Internet 连接的情况下安装 PowerShell

在离线场景中，必须先将 PowerShell 模块下载到已建立 Internet 连接的计算机，然后将其传送到 Azure Stack 开发工具包进行安装。

在具有 Internet 连接登录到计算机并使用以下脚本来下载 Azure 资源管理器和 AzureStack 包，具体取决于你的 Azure Stack 版本：

  - Azure Stack 1808 或更高版本。

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Azure Stack 1807 或更早版本。

    > [!Note]  
    若要从 1.2.11 版升级，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - 1804 或更早版本的 azure Stack。

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

2. 将下载的包复制到 USB 设备。

3. 登录到工作站，将包从 USB 设备复制到工作站中的某个位置。

4. 现在将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.配置 PowerShell 以使用代理服务器

在需要代理服务器访问 Internet 的情况下，必须首先配置 PowerShell 以使用现有的代理服务器：

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
