---
title: 有关安装和配置适用于 Azure Stack 的 PowerShell 的快速入门 | Microsoft Docs
description: 了解如何安装和配置适用于 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: db253c921354ea132dc6b043dcb6f0b96cdbfd88
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368165"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>在 Azure Stack 中使用 PowerShell 启动并运行

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本快速入门帮助你使用 PowerShell 来安装和配置 Azure Stack 环境。 本文中提供的脚本仅面向 **Azure Stack 操作员**。

本文是[安装 PowerShell]( azure-stack-powershell-install.md)、[下载工具]( azure-stack-powershell-download.md)和[配置 Azure Stack 操作员的 PowerShell 环境]( azure-stack-powershell-configure-admin.md)文章中所述步骤的精简版本。 在本文中使用的脚本，您可以设置 PowerShell 与 Azure Active Directory 或 Active Directory 联合身份验证服务 (AD FS) 部署的 Azure Stack 环境。  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>设置 PowerShell 进行基于 Azure Active Directory 的部署  

登录到 Azure Stack 开发工具包；如果已通过 VPN 建立连接，请登录到基于 Windows 的外部客户端。 打开权限提升的 PowerShell ISE 会话，并运行以下脚本。

确保根据需要更新环境配置的 **TenantName**、**ArmEndpoint** 和 **GraphAudience** 变量：

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

加载你的 Azure Stack 版本的 API 配置文件和管理员模块。

  - Azure Stack 1808 或更高版本。

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
  ```

  - Azure Stack 1807 或更早版本。

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

  - 1804 或更早版本的 azure Stack。

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11
  ```

下载 Azure Stack 工具并连接。

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>设置 PowerShell 进行基于 AD FS 的部署

如果在连接到 Internet 的情况下运行 Azure Stack，则可以使用以下脚本。 但是，如果在未建立 Internet 连接的情况下运行 Azure Stack，请使用[离线安装 PowerShell](azure-stack-powershell-install.md) 的方法，用于配置 PowerShell 的 cmdlet 与此脚本中所示相同。 登录到 Azure Stack 开发工具包；如果已通过 VPN 建立连接，请登录到基于 Windows 的外部客户端。 打开权限提升的 PowerShell ISE 会话，然后运行以下脚本。 确保根据需要更新环境配置的 **ArmEndpoint** 和 **GraphAudience** 变量：

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

加载你的 Azure Stack 版本的 API 配置文件和管理员模块。

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
    若要从 1.2.11 升级版本，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

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

下载 Azure Stack 工具并连接。

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>测试连接

配置 PowerShell 后，可以通过创建资源组来测试配置：

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> 若要指定资源组，订阅中需有一个资源组。 有关订阅的详细信息，请参阅[计划、套餐、配额和订阅概述](azure-stack-plan-offer-quota-overview.md)

创建资源组后，“Provisioning state”属性会设置为“Succeeded”。

## <a name="next-steps"></a>后续步骤

 - [安装和配置 CLI](azure-stack-connect-cli.md)
 - [开发模板](user/azure-stack-develop-templates.md)
