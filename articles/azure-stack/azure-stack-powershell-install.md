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
ms.date: 08/10/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2619f959dbefba84ea1a4d5aa974055998b78b5a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "42139336"
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

需要安装 Azure Stack 兼容的 PowerShell 模块可以使用你的云。 通过一个称为功能启用兼容性*API 配置文件*。

API 配置文件提供了一种方法来管理 Azure 和 Azure Stack 之间版本差异。 将 API 版本配置文件是一组 Azure 资源管理器 PowerShell 模块具有特定 API 版本。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持特定日期的配置文件版本等**2017年-03-09-profile**，而 Azure 则支持**最新**API 版本配置文件。 在安装配置文件时，安装对应于指定的配置文件的 Azure 资源管理器 PowerShell 模块。

在 Internet 连接的部分连接或断开连接的情况下，可以安装 Azure Stack 兼容的 PowerShell 模块。 在本文中，我们逐步安装对于这种情况适用于 Azure Stack PowerShell 的详细说明。

## <a name="1-verify-your-prerequisites"></a>1.验证你的先决条件

在 get 前开始使用 Azure Stack 和 PowerShell，你将需要有几项要求。

- **PowerShell 版本 5.0**  
若要检查版本，请运行 $PSVersionTable.PSVersion 并比较**主**版本。 如果没有 PowerShell 5.0，请单击此[链接](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)来更新到 PowerShell 5.0。

  > [!Note]  
  > PowerShell 5.0 要求 Windows 计算机。

- **运行 Powershell 提升的提示符**  
  您需要能够使用管理权限运行 PowerShell。

- **PowerShell 库访问**  
  你将需要访问[PowerShell 库](https://www.powershellgallery.com)。 库是 PowerShell 内容的中央存储库。 **PowerShellGet**模块包含用于发现、 安装、 更新和发布 PowerShell 项目，如模块、 DSC 资源、 角色功能和脚本从 PowerShell 库和其他专用的 cmdlet存储库。 如果在离线场景中使用 PowerShell，您需要检索资源从计算机连接到 Internet，并将其存储在已断开连接的计算机可以访问的位置。


<!-- Nuget? -->

## <a name="2-validate-if-the-powershell-gallery-is-accessible"></a>2.验证是否可访问 PowerShell 库

验证如果 PSGallery 注册为存储库。

> [!Note]  
> 此步骤需要访问 Internet。 

打开提升的 PowerShell 提示符，并运行以下 cmdlet:

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

1. 若要卸载现有的 AzureRM PowerShell 模块，请关闭所有活动 PowerShell 会话，并运行以下 cmdlet:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. 删除开头的所有文件夹`Azure`从`C:\Program Files\WindowsPowerShell\Modules`和`C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.Azure Stack 连接到 Internet 的连接： 安装 PowerShell

Azure Stack 需要**2017年-03-09-profile** API 版本配置文件，可通过安装**AzureRM.Bootstrapper**模块。 除了 AzureRM 模块以外，还应安装 Azure Stack 特定的 Azure PowerShell 模块。 

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

  - **版本 1.4.0** (Azure Stack 1804 或更高版本)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **版本 1.2.11** （之前 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

通过运行以下命令来确认安装：

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.未连接到 Internet 断开连接： 安装 PowerShell

在离线场景中，必须先将 PowerShell 模块下载到已建立 Internet 连接的计算机，然后将其传送到 Azure Stack 开发工具包进行安装。

登录到其中已建立 Internet 连接并使用以下脚本下载到本地计算机具体取决于你的 Azure Stack 版本上的 Azure 资源管理器和 AzureStack 包的计算机。


  - **版本 1.3.0** (Azure Stack 1804 或更高版本)
  
    > [!Note]  
    若要从 1.2.11 版升级，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **版本 1.2.11** （之前 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. 将下载的包复制到 USB 设备。

3. 登录到工作站，将包从 USB 设备复制到工作站中的某个位置。

4. 接下来，必须将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

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

在需要代理服务器访问 Internet 的情况下，必须首先配置 PowerShell 以使用现有的代理服务器。

1. 打开提升的 PowerShell 命令提示符。
2. 运行以下命令：

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>后续步骤

 - [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
 - [配置 Azure Stack 用户的 PowerShell 环境](user/azure-stack-powershell-configure-user.md)  
 - [配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md) 
 - [在 Azure Stack 中管理 API 版本配置文件](user/azure-stack-version-profiles.md)  
