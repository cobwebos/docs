---
title: "配置 Azure 堆栈用户 PowerShell 环境 |Microsoft 文档"
description: "配置 Azure 堆栈用户 PowerShell 环境"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 0bd5b4a98fee7a5d914e53e49a9517f5d3682a88
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>配置 Azure 堆栈用户 PowerShell 环境

作为 Azure 堆栈用户，你可以配置你 Azure 堆栈开发工具包 PowerShell 环境。 在配置之后，你可以使用 PowerShell 管理 Azure 堆栈的资源如订阅产品，虚拟机创建，部署 Azure 资源管理器模板，等等。本主题范围以将环境只，如果你想要设置云运算符环境中，PowerShell 是指的用户与使用[配置 Azure 堆栈运算符 PowerShell 环境](../azure-stack-powershell-configure-admin.md)文章。 

## <a name="prerequisites"></a>必备组件 

从运行以下先决条件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>配置用户环境并登录到 Azure 堆栈

基于的部署 (Azure AD 或 AD FS)，运行以下脚本以配置 PowerShell 以 Azure 堆栈 （请确保将 AAD tenantName、 GraphAudience 终结点和根据你的环境配置 ArmEndpoint 值） 之一的类型：

### <a name="azure-active-directory-aad-based-deployments"></a>部署基于 azure Active Directory (AAD)
       
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

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>部署基于 active Directory 联合身份验证服务 (AD FS) 
          
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

当在不具有通过门户部署任何资源的新创建的用户订阅上运行，不自动注册资源提供程序。 显式应通过使用以下脚本来注册它们：

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>测试连接

现在，我们具有所有内容设置，让我们使用 PowerShell 创建 Azure 堆栈中的资源。 例如，你可以创建应用程序的资源组并添加虚拟机。 使用以下命令创建名为"MyResourceGroup"的资源组：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤
* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
