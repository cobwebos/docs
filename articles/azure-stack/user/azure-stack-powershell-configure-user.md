---
title: 配置 Azure Stack 用户的 PowerShell 环境 | Microsoft Docs
description: 配置 Azure Stack 用户的 PowerShell 环境
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/26/2017
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: e17fc85de3d11034889c39fd205b7ddc8cb344cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>配置 Azure Stack 用户的 PowerShell 环境

Azure Stack 用户可以配置 Azure Stack 开发工具包的 PowerShell 环境。 配置后，可以使用 PowerShell 来管理 Azure Stack 资源，例如订阅产品、创建虚拟机、部署 Azure 资源管理器模板，等等。本主题的范围仅限用户环境，若要为云操作员环境设置 PowerShell，请参阅[配置 Azure Stack 操作员的 PowerShell 环境](../azure-stack-powershell-configure-admin.md)一文。 

## <a name="prerequisites"></a>必备组件 

如果已[通过 VPN 建立连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)，请通过[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或基于 Windows 的外部客户端运行以下先决条件操作：

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>配置用户环境并登录到 Azure Stack

根据部署类型（Azure AD 或 AD FS），运行以下脚本之一配置适用于 Azure Stack 的 PowerShell（请确保根据自己的环境配置替换 AAD tenantName、GraphAudience 终结点和 ArmEndpoint 的值）：

### <a name="azure-active-directory-aad-based-deployments"></a>基于 Azure Active Directory (AAD) 的部署
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>基于 Active Directory 联合身份验证服务 (AD FS) 的部署 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>注册资源提供程序

当尚未通过门户部署任何资源的新建用户订阅中操作时，不会自动注册资源提供程序。 应使用以下脚本显式注册资源提供程序：

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，让我们使用 PowerShell 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤
* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
