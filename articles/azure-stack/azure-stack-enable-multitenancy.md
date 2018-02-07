---
title: "启用 Azure 堆栈中的多租户 |Microsoft 文档"
description: "了解如何支持 Azure 堆栈中的多个 Azure Active Directory 目录"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: a2cba85a553f20040d2fb118b35859b05870e361
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>启用 Azure 堆栈中的多租户

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以配置 Azure 堆栈，以支持来自多个 Azure Active Directory (Azure AD) 租户，若要使用 Azure 堆栈中的服务的用户。 例如，考虑以下方案：

 - 你是 contoso.onmicrosoft.com，服务管理员 Azure 堆栈的安装位置。
 - Mary 是 fabrikam.onmicrosoft.com 的，目录管理员是 fabrikam.onmicrosoft.com 的来宾用户所在的位置。 
 - Mary 的公司收到你的公司，IaaS 和 PaaS 服务，并需要允许来宾目录 (fabrikam.onmicrosoft.com) 中的用户登录并在 contoso.onmicrosoft.com 中使用 Azure 堆栈的资源。

本指南提供了所需的步骤，在此方案中，上下文中配置多租户 Azure 堆栈中。  在此方案中，你和 Mary 必须完成步骤以使用户从 Fabrikam 登录并使用从 Azure 堆栈部署在 Contoso 中的服务。  

## <a name="before-you-begin"></a>开始之前
有几个先决条件，来应对之前在 Azure 堆栈中配置多租户：
  
 - 你和 Mary 必须协调跨 Azure 堆栈 (Contoso) 中的安装的目录和来宾目录 (Fabrikam) 的管理步骤。  
 - 请确保你已[安装](azure-stack-powershell-install.md)和[配置](azure-stack-powershell-configure-admin.md)PowerShell for Azure 堆栈。
 - [下载 Azure 堆栈 Tools](azure-stack-powershell-download.md)，并将连接和标识模块导入：

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary 将需要[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)到 Azure 堆栈的访问。 

## <a name="configure-azure-stack-directory"></a>配置 Azure 堆栈目录
在本部分中，你将配置为允许来自 Fabrikam Azure AD 目录租户的登录的 Azure 堆栈。

### <a name="onboard-guest-directory-tenant"></a>板载来宾 directory 租户
下一步上, 架到 Azure 堆栈来宾目录租户 (Fabrikam)。  此步骤配置为接受用户和来宾 directory 租户中的服务主体的 Azure 资源管理器。

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local"
````



## <a name="configure-guest-directory"></a>配置来宾目录
完成 Azure 堆栈目录中的步骤后，Mary 必须向 Azure 堆栈访问来宾目录提供同意的情况下，并使用来宾目录注册 Azure 堆栈。 

### <a name="registering-azure-stack-with-the-guest-directory"></a>向来宾目录注册 Azure 堆栈
一旦来宾目录管理员已提供 Azure 堆栈，以访问 Fabrikam 的目录的许可，它们必须为 Fabrikam 的 directory 租户注册 Azure 堆栈。

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>直接用户登录
现在，你和 Mary 已完成到板载 Mary 目录的步骤，Mary 可以直接 Fabrikam 用户登录。  Fabrikam 用户 （即，具有 fabrikam.onmicrosoft.com 后缀的用户） 通过访问 https://portal.local.azurestack.external 登录。  

Mary 将指示任何[外部主体](../active-directory/active-directory-understanding-resource-access.md)Fabrikam 目录 （即，而不使用后缀 fabrikam.onmicrosoft.com Fabrikam 目录中的用户） 中使用 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com 进行登录。如果它们不使用此 URL，它们发送到其默认目录 (Fabrikam)，并收到一个错误，指出不同意其管理员。

## <a name="next-steps"></a>后续步骤

- [管理委派的提供程序](azure-stack-delegated-provider.md)
- [Azure 堆栈的关键概念](azure-stack-key-features.md)
