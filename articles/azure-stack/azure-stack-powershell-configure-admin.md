---
title: "配置 Azure 堆栈运算符 PowerShell 环境 |Microsoft 文档"
description: "了解如何配置 Azure 堆栈运算符 PowerShell 环境。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: mabrigg
ms.openlocfilehash: 96ce59d0390affaa57d05d6d08657f5c1a3c466a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>配置 Azure 堆栈运算符 PowerShell 环境

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为 Azure 堆栈操作员，你可以配置你 Azure 堆栈开发工具包 PowerShell 环境。 在配置之后，你可以使用 PowerShell 来管理 Azure 堆栈的资源，如创建产品/服务、 计划、 配额管理警报，等等。本主题范围用于环境只，如果你想要设置用户环境中，PowerShell 是指，云操作员[配置 Azure 堆栈用户 PowerShell 环境](user/azure-stack-powershell-configure-user.md)主题。 

## <a name="prerequisites"></a>必备组件

从运行以下先决条件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>配置运算符环境并登录到 Azure 堆栈

根据部署 (Azure AD 或 AD FS)，运行以下脚本来配置 Azure 堆栈运算符环境使用 PowerShell （请确保将 AAD tenantName、 GraphAudience 终结点和根据你的环境的 ArmEndpoint 值之一的类型配置）：

### <a name="azure-active-directory-aad-based-deployments"></a>部署基于 azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience 

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>部署基于 active Directory 联合身份验证服务 (AD FS)
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


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

现在，我们具有设置的所有内容，让我们使用 PowerShell 创建 Azure 堆栈中的资源。 例如，你可以创建应用程序的资源组并添加虚拟机。 使用以下命令创建名为"MyResourceGroup"的资源组：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤
* [为 Azure Stack 开发模板](user/azure-stack-develop-templates.md)
* [通过 PowerShell 部署模板](user/azure-stack-deploy-template-powershell.md)
