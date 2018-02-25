---
title: "标识 Azure 堆栈的体系结构 |Microsoft 文档"
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
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>标识 Azure 堆栈的体系结构
选择要与 Azure 堆栈使用的标识提供程序之前，了解 Azure Active Directory (Azure AD) 的选项和 Active Directory 联合服务 (AD FS) 的重要区别。 

## <a name="capabilities-and-limitations"></a>功能和限制 
你选择的标识提供程序可以限制所选项，包括对多租户支持。 

  

|功能或方案        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|连接到 internet     |是       |可选|
|适用于多组织支持     |是       |否      |
|应用商店联合       |是       |是-要求使用[脱机 Marketplace 联合](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity)工具。|
|Active Directory 身份验证库 (ADAL) 的支持 |是 |是|
|Azure 命令行界面 (CLI)，Visual Studio (VS) 和 PowerShell 之类的工具的支持  |是 |是|
|创建服务主体通过门户     |是 |否|
|使用证书创建服务主体      |是 |是|
|使用机密 （键） 创建服务主体    |是 |否|
|应用程序可以使用 Graph 服务           |是 |否|
|应用程序可用于标识提供程序登录 |是 |是-要求应用程序进行与你的本地联合 AD FS。 |

## <a name="topologies"></a>拓扑
以下部分提供有关你可以使用的标识拓扑的信息。

### <a name="azure-ad--single-tenant"></a>Azure AD-单租户 
默认情况下，当你安装 Azure 堆栈，并使用 Azure AD 中，Azure 堆栈使用单租户拓扑。 

单租户拓扑非常有用：
- 所有用户都都在同一租户的一部分。
- 服务提供商托管的组织程序 Azure 堆栈实例。  

![单租户拓扑使用 Azure AD 的 azure 堆栈拓扑](media/azure-stack-identity-architecture/single-tenant.png)

采用此拓扑：
- Azure 堆栈注册所有应用程序和服务添加到同一 Azure AD 租户目录。 
- Azure 堆栈进行身份验证的用户和应用程序从该目录，包括令牌。 
- 管理员 （云操作员） 和租户用户的标识是在相同的目录租户。 
- 若要使另一个目录中的用户能够访问此 Azure 堆栈环境，你必须[邀请用户以来宾身份](azure-stack-identity-overview.md#guest-users)到租户目录。  

### <a name="azure-ad--multi-tenant"></a>Azure AD-多租户
云操作员可以配置 Azure 堆栈，以允许从一个或多个组织的租户的应用程序的访问。 用户访问应用程序通过用户门户。 在此配置中，管理门户 （由，云操作员） 不能超过向用户从单个目录。 

多租户拓扑非常有用：
- 服务提供商想要允许多个组织中的用户访问 Azure 堆栈。

![使用 Azure AD 的多租户拓扑的 azure 堆栈拓扑](media/azure-stack-identity-architecture/multi-tenant.png)

采用此拓扑：
- 对资源的访问应在每个组织基础上。 
- 从一个组织的用户应不能向其组织外部用户授予对资源的访问。  
- 管理员 （云操作员） 的标识可以是用户的标识比单独的目录租户中。 这种分离提供的帐户的标识提供程序级别隔离。 
 
### <a name="ad-fs"></a>AD FS  
时，AD FS 拓扑是所必需的下列条件的任何一种情况：
- Azure 堆栈不连接到 Internet。
- Azure 堆栈可以连接到 Internet，但你选择为标识提供程序使用 AD FS。
  
![使用 AD FS 的 azure 堆栈拓扑](media/azure-stack-identity-architecture/adfs.png)

采用此拓扑：
- 若要支持使用在生产中，必须将内置的 Azure 堆栈 AD FS 实例集成使用现有 AD FS 实例支持的 Active Directory (AD)，通过联合信任。 
- 你可以与你现有的 AD 集成 Azure 堆栈中的 Graph 服务。  你还可以使用 OData 基于支持 Api 与 Azure AD Graph API 相一致的 Graph API 服务。  

  若要与你的 AD 交互，Graph API 需要具有只读权限的用户凭据从你的 AD AD。 
  - 内置的 AD FS 基于 Server 2016。 
  - Server 2012 或更低版本，则必须基于你的 AD FS 和 AD。 
  
  你的 AD，内置的 AD FS，交互不限于 OpenID Connect，并且可以使用任何相互支持的协议。  
  - 用户帐户创建和管理在你的本地 AD。
  - 在内置 AD 中管理服务主体和应用程序的注册。



## <a name="next-steps"></a>后续步骤
- [标识概述](azure-stack-identity-overview.md)   
- [数据中心集成的标识](azure-stack-integrate-identity.md)