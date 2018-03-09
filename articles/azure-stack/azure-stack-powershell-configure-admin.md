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
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: 57aa5a1ccc45548c3e789b50c888f669df39d5f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>配置 Azure 堆栈运算符 PowerShell 环境

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以配置要使用 PowerShell 来管理资源，例如创建产品/服务、 计划、 配额和警报的 Azure 堆栈。 本主题将帮助你配置运算符环境。 如果你想要配置 PowerShell 以用户环境，请参阅到[配置 Azure 堆栈用户 PowerShell 环境](user/azure-stack-powershell-configure-user.md)文章。

## <a name="prerequisites"></a>必备组件

从运行以下先决条件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>配置运算符环境并登录到 Azure 堆栈

使用 PowerShell 配置 Azure 堆栈运算符环境。 基于类型的部署，Azure AD 或 AD FS，运行以下脚本之一： 将 Azure AD tenantName、 GraphAudience 终结点和 ArmEndpoint 值替换为你自己的环境配置。

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (Azure AD) 基于部署

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>部署基于 active Directory 联合身份验证服务 (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>测试连接

现在，你具有的所有内容设置，让我们使用 PowerShell 创建 Azure 堆栈中的资源。 例如，你可以创建应用程序的资源组并添加虚拟机。 使用以下命令创建名为"MyResourceGroup"的资源组：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤
* [为 Azure Stack 开发模板](user/azure-stack-develop-templates.md)
* [通过 PowerShell 部署模板](user/azure-stack-deploy-template-powershell.md)
