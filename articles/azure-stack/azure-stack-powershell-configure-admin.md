---
title: 以操作员身份使用 PowerShell 连接到 Azure Stack | Microsoft Docs
description: 了解如何以操作员身份使用 PowerShell 连接到 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: f5df27df5901d6568b30e1ae4f40cae8b3de4c86
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298813"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>以操作员身份使用 PowerShell 连接到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以将 Azure Stack 配置为使用 PowerShell 来管理资源，例如创建套餐、计划、配额以及警报。 本主题有助于配置操作员环境

## <a name="prerequisites"></a>必备组件

运行以下先决条件[开发工具包](.\asdk\asdk-connect.md#connect-with-rdp)或基于 Windows 的外部客户端是否[连接到通过 VPN ASDK](.\asdk\asdk-connect.md#connect-with-vpn)。 

 - 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
 - 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>配置操作员环境并登录到 Azure Stack

使用 PowerShell 配置 Azure Stack 操作员环境。 运行以下脚本之一：将 Azure AD tenantName、GraphAudience 终结点和 ArmEndpoint 的值替换为你自己的环境配置。

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRmEnvironment -Name "AzureStackAdmin" -ARMEndpoint $ArmEndpoint -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external


    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>测试连接

完成所有设置后，请使用 PowerShell 在 Azure Stack 中创建资源。 例如，可以为应用程序创建资源组并添加虚拟机。 使用以下命令创建名为“MyResourceGroup”的资源组。

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>后续步骤

 - [为 Azure Stack 开发模板](user/azure-stack-develop-templates.md)
 - [通过 PowerShell 部署模板](user/azure-stack-deploy-template-powershell.md)