---
title: "安装和配置 PowerShell 以 Azure 堆栈快速入门 |Microsoft 文档"
description: "了解如何安装和配置 PowerShell 的 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.openlocfilehash: cba6f8295e5d4b75192e566d4931cbd617e7dc8d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>获取启动并运行 Azure 堆栈中的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本快速入门教程可帮助你安装和使用 PowerShell 配置 Azure 堆栈环境。 我们在本文中提供的脚本作用域为**Azure 堆栈运算符**仅。

本文是中所述的步骤的简要的版本[安装 PowerShell]( azure-stack-powershell-install.md)，[下载工具]( azure-stack-powershell-download.md)，和[配置 Azure 堆栈运算符 PowerShell 环境]( azure-stack-powershell-configure-admin.md)文章。 通过使用本主题中的脚本，你可以将设置 PowerShell 为 Azure 堆栈环境与 Azure Active Directory 或 Active Directory 联合身份验证服务 (AD FS) 部署。  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>基于 Azure Active Directory 的部署设置 PowerShell

如果你通过 VPN 连接，登录到 Azure 堆栈开发工具包中或基于 Windows 的外部客户端。 打开提升的 PowerShell ISE 会话，，然后运行以下脚本。 请确保更新**TenantName**， **ArmEndpoint**，和**GraphAudience**根据你环境的配置变量：

> [!IMPORTANT]
> AzureRM 1.2.11 PowerShell 模块的版本附带的重大更改的列表。 若要从 1.2.10 升级版本，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>为 AD FS 基于部署设置 PowerShell

如果你正在 Azure 堆栈连接到 internet 时，可以使用以下脚本。 但是如果你正在 Azure 堆栈没有 internet 连接，使用[断开连接的安装 PowerShell 的方式](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)和配置 PowerShell 的 cmdlet 将保持相同，此脚本中所示。 如果你通过 VPN 连接，登录到 Azure 堆栈开发工具包中或基于 Windows 的外部客户端。 打开提升的 PowerShell ISE 会话，，然后运行以下脚本。 请确保更新**ArmEndpoint**和**GraphAudience**根据你环境的配置变量：

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>测试连接

现在，你已配置 PowerShell，你可以通过创建资源组来测试配置：

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

创建资源组后，**预配状态**属性设置为**Succeeded**。

## <a name="next-steps"></a>后续步骤

* [安装和配置 CLI](azure-stack-connect-cli.md)

* [开发模板](user/azure-stack-develop-templates.md)







