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
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>配置 Azure Stack 用户的 PowerShell 环境

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用本文中说明来配置 Azure 堆栈用户 PowerShell 环境。
你配置环境后，你可以使用 PowerShell 来管理 Azure 堆栈的资源。 例如，可以使用 PowerShell 以订阅产品/服务、 创建虚拟机，并将 Azure 资源管理器模板部署。

>[!NOTE]
>这篇文章针对 Azure 堆栈用户环境范围内。 如果你想要设置云运算符环境 PowerShell，请参阅[配置 Azure 堆栈运算符 PowerShell 环境](../azure-stack-powershell-configure-admin.md)文章。

## <a name="prerequisites"></a>必备组件

你可以配置从这些系统必备组件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>配置用户环境并登录到 Azure Stack

基于你的 Azure 堆栈部署 (Azure AD 或 AD FS)，运行以下脚本以配置 PowerShell 以 Azure 堆栈之一的类型。

请确保从 Azure 堆栈配置的以下脚本变量将替换值：

* AAD tenantName
* GraphAudience 终结点
* ArmEndpoint

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

资源提供程序将不为没有通过门户部署任何资源的新用户订阅自动注册。 你可以显式注册资源提供程序，通过运行以下脚本：

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>测试连接

当全部内容设置之后时，通过使用 PowerShell 在 Azure 堆栈中创建资源测试连接性。 为测试中，创建应用程序的资源组并添加虚拟机。 运行以下命令以创建一个名为"MyResourceGroup"的资源组：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤

* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
