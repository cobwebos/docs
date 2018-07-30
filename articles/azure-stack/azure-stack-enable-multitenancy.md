---
title: Azure Stack 中的多租户
description: 了解如何在 Azure Stack 中支持多个 Azure Active Directory 目录
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: patricka
ms.openlocfilehash: e61b4457cd88c236145ce7595ee7db4340538465
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331128"
---
# <a name="multi-tenancy-in-azure-stack"></a>Azure Stack 中的多租户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以配置 Azure Stack，以支持多个 Azure Active Directory (Azure AD) 租户中的用户使用 Azure Stack 中的服务。 例如，考虑以下方案：

 - 你是 contoso.onmicrosoft.com，服务管理员 Azure Stack 的安装位置。
 - Mary 是 fabrikam.onmicrosoft.com，目录管理员来宾用户的位置。 
 - Mary 的公司收到你的公司，IaaS 和 PaaS 服务，并需要允许来宾目录 (fabrikam.onmicrosoft.com) 中的用户登录并在 contoso.onmicrosoft.com 中使用 Azure Stack 资源。

本指南提供了此方案上下文中所需的步骤，用于在 Azure Stack 中配置多租户。 在此方案中，你和 Mary 必须完成相关步骤以使 Fabrikam 中的用户能够登录并使用 Contoso 中部署的 Azure Stack 提供的服务。  

## <a name="enable-multi-tenancy"></a>启用多租户

在 Azure Stack 中配置多租户之前，需要考虑几个先决条件：
  
 - 你和 Mary 必须在安装 Azure Stack 的目录 (Contoso) 和来宾目录 (Fabrikam) 之间协调管理步骤。  
 - 确保已[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-admin.md)适用于 Azure Stack 的 PowerShell。
 - [下载 Azure Stack Tools](azure-stack-powershell-download.md)，并导入“连接和标识”模块：

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

 - Mary 将需要 Azure Stack 的 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 访问权限。 

### <a name="configure-azure-stack-directory"></a>配置 Azure Stack 目录

在本部分中，将配置 Azure Stack 以允许从 Fabrikam Azure AD 目录租户登录。

加入来宾目录租户 (Fabrikam) 到 Azure Stack 通过配置以接受用户和服务主体从来宾目录租户的 Azure 资源管理器。

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>配置来宾目录

完成 Azure Stack 目录中的步骤后，Mary 必须允许 Azure Stack 访问来宾目录，并将 Azure Stack 注册到来宾目录。 

#### <a name="registering-azure-stack-with-the-guest-directory"></a>将 Azure Stack 注册到来宾目录

来宾目录管理员允许 Azure Stack 访问 Fabrikam 的目录后，Mary 必须将 Azure Stack 注册到 Fabrikam 的目录租户。

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> 如果你的 Azure Stack 管理员安装新的服务或更新以后，可能需要再次运行此脚本。
>
> 在任何时间来检查你的目录中的 Azure Stack 应用程序的状态再次运行此脚本。

### <a name="direct-users-to-sign-in"></a>指导用户登录

现在，你和 Mary 已完成到加入 Mary 目录的步骤，Mary 可以指导 Fabrikam 用户登录。  Fabrikam 用户 （即，具有 fabrikam.onmicrosoft.com 后缀的用户） 登录，请访问https://portal.local.azurestack.external。  

Mary 将指导任何[外部主体](../role-based-access-control/rbac-and-directory-admin-roles.md)Fabrikam 目录 （即，如果没有 fabrikam.onmicrosoft.com 后缀，Fabrikam 目录中的用户） 中使用登录https://portal.local.azurestack.external/fabrikam.onmicrosoft.com。  如果他们不使用此 URL，则将被发送到其默认目录 (Fabrikam)，并收到一个错误，指出其管理员未许可。

## <a name="disable-multi-tenancy"></a>禁用多租户

如果你不希望再在 Azure Stack 中的多个租户，可以通过执行以下步骤顺序禁用多租户：

1. 以管理员身份的来宾目录 (在此方案中为 Mary)，运行*注销 AzsWithMyDirectoryTenant*。 Cmdlet 卸载所有 Azure Stack 应用程序中的新目录。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. 作为运行 （在此方案中您），Azure Stack 的服务管理员*注销 AzSGuestDirectoryTenant*。 

    ``` PowerShell  
    ## The following Azure Resource Manaager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > 禁用多租户步骤必须按顺序执行。 步骤 #1 失败，如果第一次完成步骤 #2。

## <a name="next-steps"></a>后续步骤

- [管理委派提供程序](azure-stack-delegated-provider.md)
- [Azure Stack 关键概念](azure-stack-key-features.md)
