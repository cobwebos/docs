---
title: 配置 Azure Stack 用户的 PowerShell 环境 | Microsoft Docs
description: 配置 Azure Stack 用户的 PowerShell 环境
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: d8b245666989552208f8cbcf0dddfdfc310f65e0
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "42139493"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>配置 Azure Stack 用户的 PowerShell 环境

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文为您提供用于连接到 Azure Stack 实例的步骤。 必须连接到 Azure Stack 使用 PowerShell 管理资源。 例如，可以使用 PowerShell 来订阅套餐、创建虚拟机和部署 Azure 资源管理器模板。 若要执行 PowerShell cmdlet。

若要获取设置：
  - 请确保您有要求。
  - 连接与 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS)。 
  - 注册资源提供程序。
  - 测试连接。

## <a name="prerequisites"></a>必备组件

如果已[通过 VPN 建立连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)，请通过[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或基于 Windows 的外部客户端配置以下先决条件：

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

确保将以下脚本变量替换为 Azure Stack 配置中的值：

- **Azure AD 租户名称**  
  用于管理 Azure Stack 中，例如，yourdirectory.onmicrosoft.com Azure AD 租户的名称。
- **Azure 资源管理器终结点**  
  对于 Azure Stack 开发工具包，此值设置为https://management.local.azurestack.external。 若要获取此值对于 Azure Stack 集成系统，请联系服务提供商。

## <a name="connect-with-azure-ad"></a>与 Azure AD 连接

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>连接使用 AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId
  ```

## <a name="register-resource-providers"></a>注册资源提供程序

不会自动为没有通过门户部署任何资源的新用户订阅自动注册资源提供程序。 可以通过运行以下脚本显式注册资源提供程序：

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，请通过使用 PowerShell 在 Azure Stack 中创建资源来测试连接。 作为测试，为应用程序创建资源组并添加虚拟机。 运行以下命令创建名为“MyResourceGroup”的资源组：

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
- 若要为云操作员环境设置 PowerShell，请参阅[配置 Azure Stack 操作员的 PowerShell 环境](../azure-stack-powershell-configure-admin.md)一文。