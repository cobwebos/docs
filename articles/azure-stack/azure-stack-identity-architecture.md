---
title: "Azure Stack 的标识体系结构 | Microsoft Docs"
description: "了解可以采用与 Azure 堆栈的标识基础结构。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 899e0fc0c1eb93d68c79c92c9cc042462ebc2fef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Azure Stack 的标识体系结构
选择要与 Azure 堆栈使用的标识提供程序之前，了解 Azure Active Directory (Azure AD) 的选项和 Active Directory 联合身份验证服务 (AD FS) 的重要区别。 

## <a name="capabilities-and-limitations"></a>功能和限制 
你选择的标识提供程序可以限制所选项，包括对多租户支持。 

  

|功能或方案        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|连接到 Internet     |是       |可选|
|对多租户的支持     |是       |否      |
|Marketplace 联合       |是       |可以。 需要使用[脱机 Marketplace 联合](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)工具。|
|对 Active Directory 身份验证库 (ADAL) 的支持 |是 |是|
|对 Azure CLI，Visual Studio 和 PowerShell 等工具的支持  |是 |是|
|创建服务主体通过 Azure 门户     |是 |否|
|使用证书创建服务主体      |是 |是|
|包含密码 （密钥） 中创建服务主体    |是 |否|
|应用程序可以使用 Graph 服务           |是 |否|
|应用程序可以将标识提供程序用于登录 |是 |可以。 要求应用程序进行联合与本地 AD FS 实例。 |

## <a name="topologies"></a>拓扑
下列部分一讨论你可以使用的各种标识拓扑。

### <a name="azure-ad-single-tenant-topology"></a>Azure AD： 单租户拓扑 
默认情况下，当安装 Azure Stack 并使用 Azure AD 时，Azure Stack 使用单租户拓扑。 

单租户拓扑非常适用于下列情况：
- 所有用户都属于同一租户。
- 服务提供程序托管着组织的 Azure Stack 实例。 

![使用 Azure AD 的 azure 堆栈单租户拓扑](media/azure-stack-identity-architecture/single-tenant.png)

此拓扑特点是具有以下特征：
- Azure Stack 将所有应用程序和服务注册到同一 Azure AD 租户目录。 
- Azure 堆栈进行身份验证的用户和应用程序从该目录，包括令牌。 
- 管理员（云操作员）和租户用户的标识位于同一目录租户中。 
- 若要使其他目录中的用户能够访问此 Azure Stack 环境，必须[将用户作为来宾邀请](azure-stack-identity-overview.md#guest-users)到该租户目录。 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD： 多租户拓扑
云操作员可以将 Azure Stack 配置为允许一个或多个组织中的租户访问应用程序。 用户通过用户门户访问应用程序。 在此配置中，管理员门户（由云操作员使用）仅限单个目录中的用户访问。 

多租户拓扑非常适用于下列情况：
- 服务提供商希望允许多个组织中的用户访问 Azure Stack。

![使用 Azure AD 的 azure 堆栈的多租户拓扑](media/azure-stack-identity-architecture/multi-tenant.png)

此拓扑特点是具有以下特征：
- 对资源的访问权限应当以组织为单位。 
- 从一个组织的用户应无法将其组织外部的用户授予对资源的访问。 
- 管理员 （云操作员） 的标识可以是从用户的标识单独的目录租户中。 此分离在标识提供者级别提供了帐户隔离。 
 
### <a name="ad-fs"></a>AD FS  
当以下任一情况属实时，需要使用 AD FS 拓扑：
- Azure 堆栈不连接到 internet。
- Azure 堆栈可以连接到 internet，但你选择为标识提供程序使用 AD FS。
  
![使用 AD FS 的 Azure Stack 拓扑](media/azure-stack-identity-architecture/adfs.png)

此拓扑特点是具有以下特征：
- 若要在生产环境中支持此拓扑的使用，必须将与一个现有的 AD FS 实例，它由通过联合信任的 Active Directory 中，内置的 Azure 堆栈 AD FS 实例的集成。 
- 你可以与你现有的 Active Directory 实例集成 Azure 堆栈中的 Graph 服务。 你也可以使用支持与 Azure AD Graph API 相一致的 Api 基于 OData 的 Graph API 服务。 

  要与 Active Directory 实例进行交互，Graph API 需要从 Active Directory 实例具有只读权限的用户凭据。 
  - 内置的 AD FS 实例基于 Windows Server 2016。 
  - Windows Server 2012 或更高版本，则必须基于你的 AD FS 和 Active Directory 实例。 
  
  Active Directory 实例，内置的 AD FS 实例，交互不是限于 OpenID Connect，并且它们可以使用任何共同支持的协议。 
  - 用户帐户创建和管理你的本地 Active Directory 实例中。
  - 内置的 Active Directory 实例中管理服务主体和应用程序的注册。



## <a name="next-steps"></a>后续步骤
- [标识概述](azure-stack-identity-overview.md)   
- [数据中心集成的标识](azure-stack-integrate-identity.md)
