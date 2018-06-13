---
title: 在 Azure Stack 中启用多租户 | Microsoft Docs
description: 了解如何在 Azure Stack 中支持多个 Azure Active Directory 目录
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: mabrigg
ms.openlocfilehash: 59b0f8e4c7234b246d4fb54d065ff318939e2662
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301829"
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>在 Azure Stack 中启用多租户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以配置 Azure Stack，以支持多个 Azure Active Directory (Azure AD) 租户中的用户使用 Azure Stack 中的服务。 例如，考虑以下方案：

 - 你是 contoso.onmicrosoft.com，服务管理员 Azure 堆栈的安装位置。
 - Mary 是 fabrikam.onmicrosoft.com 的，目录管理员是 fabrikam.onmicrosoft.com 的来宾用户所在的位置。 
 - Mary 的公司收到你的公司，IaaS 和 PaaS 服务，并需要允许来宾目录 (fabrikam.onmicrosoft.com) 中的用户登录并在 contoso.onmicrosoft.com 中使用 Azure 堆栈的资源。

本指南提供了此方案上下文中所需的步骤，用于在 Azure Stack 中配置多租户。  在此方案中，你和 Mary 必须完成相关步骤以使 Fabrikam 中的用户能够登录并使用 Contoso 中部署的 Azure Stack 提供的服务。  

## <a name="before-you-begin"></a>开始之前
在 Azure Stack 中配置多租户之前，需要考虑几个先决条件：
  
 - 你和 Mary 必须在安装 Azure Stack 的目录 (Contoso) 和来宾目录 (Fabrikam) 之间协调管理步骤。  
 - 确保已[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-admin.md)适用于 Azure Stack 的 PowerShell。
 - [下载 Azure Stack Tools](azure-stack-powershell-download.md)，并导入“连接和标识”模块：

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary 将需要 Azure Stack 的 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 访问权限。 

## <a name="configure-azure-stack-directory"></a>配置 Azure Stack 目录
在本部分中，将配置 Azure Stack 以允许从 Fabrikam Azure AD 目录租户登录。

### <a name="onboard-guest-directory-tenant"></a>加入来宾目录租户
接下来，将来宾目录租户 (Fabrikam) 加入到 Azure Stack。  此步骤将 Azure 资源管理器配置为接受来自来宾目录租户的用户和服务主体。

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>配置来宾目录
完成 Azure Stack 目录中的步骤后，Mary 必须允许 Azure Stack 访问来宾目录，并将 Azure Stack 注册到来宾目录。 

### <a name="registering-azure-stack-with-the-guest-directory"></a>将 Azure Stack 注册到来宾目录
一旦来宾目录管理员已提供 Azure 堆栈，以访问 Fabrikam 的目录的同意，Mary 必须注册 Fabrikam 的目录租户的 Azure 堆栈。

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>指导用户登录
现在，你和 Mary 已完成到加入 Mary 目录的步骤，Mary 可以指导 Fabrikam 用户登录。  Fabrikam 用户 （即，具有 fabrikam.onmicrosoft.com 后缀的用户） 登录，请访问https://portal.local.azurestack.external。  

Mary 将指示任何[外部主体](../role-based-access-control/rbac-and-directory-admin-roles.md)Fabrikam 目录 （即，而不使用后缀 fabrikam.onmicrosoft.com Fabrikam 目录中的用户） 中使用登录https://portal.local.azurestack.external/fabrikam.onmicrosoft.com。如果他们不使用此 URL，则将被发送到其默认目录 (Fabrikam)，并收到一个错误，指出其管理员未许可。

## <a name="next-steps"></a>后续步骤

- [管理委派提供程序](azure-stack-delegated-provider.md)
- [Azure Stack 关键概念](azure-stack-key-features.md)
