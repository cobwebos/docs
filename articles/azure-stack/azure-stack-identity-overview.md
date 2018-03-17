---
title: "概述 Azure 堆栈的标识 |Microsoft 文档"
description: "了解可用于 Azure 堆栈的标识系统。"
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
ms.openlocfilehash: deebe5d8ff4c35c4507d2daf5c15025a1810d865
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>标识 Azure 堆栈的概述

Azure 堆栈需要 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS)，由 Active Directory 标识提供程序支持。 提供程序的选择是时首次部署 Azure 堆栈进行一次性决策。 概念和这篇文章中的授权详细信息可以帮助你标识提供程序之间进行选择。 

可能由在其中部署 Azure 堆栈的模式确定你选择的是 Azure AD 或 AD FS: 
- 在连接模式中部署它时，可以使用 Azure AD 或 AD FS。 
- 当你将其部署在断开连接模式下，未连接到 internet，支持仅 AD FS。

有关详细信息的选项，具体取决于你的 Azure 堆栈环境，请参阅以下文章：
- Azure 堆栈部署工具包：[标识注意事项](azure-stack-datacenter-integration.md#identity-considerations)。
- Azure 堆栈集成系统：[部署规划 Azure 堆栈的决策集成系统](azure-stack-deployment-decisions.md)。

 
## <a name="common-concepts-for-identity"></a>标识的基本概念
下一步的各节讨论有关标识提供程序和 Azure 堆栈中的使用它们的基本概念。

![标识提供程序的术语](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>目录租户和组织
目录是保留用户、应用程序、组和服务主体相关信息的容器。
 
目录租户是*组织*，如 Microsoft 或你自己的公司。 
- Azure AD 支持多个租户，并且它可支持多个组织，其自己的目录中的每个。 如果使用 Azure AD 并且有多个租户，则可以授权应用程序和用户从一个租户访问同一目录中的其他租户。
- AD FS 支持单个租户，因此，单个组织。 

### <a name="users-and-groups"></a>用户和组
用户帐户 （标识） 是使用用户 ID 和密码个人进行身份验证的标准帐户。 组可以包含用户或其他组。 

创建和管理用户与组的方式取决于所用的标识解决方案。 

在 Azure Stack 中，用户帐户： 
- 以 *username@domain* 格式创建。 尽管 AD FS 用户帐户映射到 Active Directory 实例，AD FS 不支持使用*\<域 >\<别名 >*格式。 
- 可以设置为使用多重身份验证。 
- 仅限于在其中它们首先注册，这是其组织的目录的目录。
- 可从本地目录导入。 有关详细信息，请参阅[将你的本地目录与 Azure Active Directory 集成](/azure/active-directory/connect/active-directory-aadconnect)。 

当你登录到你的组织租户门户时，你使用 *https://portal.local.azurestack.external*  URL。 

### <a name="guest-users"></a>来宾用户
来宾用户是其他目录租户中的用户帐户，这些用户已获得目录中资源的访问权限。 若要支持来宾用户，可以使用 Azure AD，并启用对多租户支持。 启用支持后，你可以邀请来宾用户能够访问在你的目录租户中，转而又可与外部组织其协作的资源。 

若要邀请来宾用户，云操作员和用户可以使用[Azure AD B2B 协作](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。 受邀的用户获取对文档、 资源和应用程序从你的目录的访问权限并维护对你自己的资源和数据的控制。 

作为来宾用户，你可以登录到另一个组织的目录租户。 为此，应将该组织的目录名称追加到门户 URL。 例如，如果你属于 Contoso 组织，并想要登录到 Fabrikam 目录，则使用https://portal.local.azurestack.external/fabrikam.onmicrosoft.com。

### <a name="applications"></a>应用程序
你可以注册应用程序到 Azure AD 或 AD FS，，然后在组织中提供了应用程序与用户。 

应用程序包括：
- **Web 应用程序**： 示例包括的 Azure 门户和 Azure 资源管理器。 它们支持 Web API 调用。 
- **本机客户端**： 示例包括 Azure PowerShell、 Visual Studio 和 Azure CLI。

应用程序可支持两种租户： 
- **单租户**： 支持用户和服务只能从注册该应用程序的相同目录。 

  > [!NOTE]    
  > 由于 AD FS 支持单个目录，应用程序创建的 AD FS 拓扑中是，根据设计，单租户应用程序。

- **多租户**： 支持使用由用户和来自注册该应用程序的目录和其他租户目录服务。 另一个租户目录（另一个 Azure AD 租户）的用户可以使用多租户应用程序登录到应用程序。 

  有关多租户的详细信息，请参阅[启用多租户](azure-stack-enable-multitenancy.md)。 

  有关如何开发多租户应用的详细信息，请参阅[多租户应用](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)。

注册应用程序时，你将创建两个对象：

- **应用程序对象**： 跨所有租户应用程序的全局表示。 此关系是一对一的软件应用程序，只在首次注册该应用程序目录中存在。

- **服务主体对象**： 为第一次注册该应用程序的目录中的应用程序创建的凭据。 服务主体也是在使用该应用程序的其他每个租户的目录中创建的。 此关系可以是一个对多的软件应用程序。 

若要了解有关应用程序和服务主体对象的详细信息，请参阅[应用程序和 Azure Active Directory 中的服务主体对象](/azure/active-directory/develop/active-directory-application-objects)。 

### <a name="service-principals"></a>服务主体 
服务主体是应用程序或服务的一组凭据，可授予 Azure Stack 中资源的访问权限。 服务主体使用分隔的用户的权限从应用程序的应用程序权限。

服务主体是在使用应用程序的每个租户中创建的。 服务主体建立用于在登录和对资源 （如用户） 保护该租户的访问权限的标识。 

- 单租户应用程序在其首次创建所在的目录中只有一个服务主体。 此服务主体创建并予以许可到正在使用的应用程序的注册过程。 
- 多租户 web 应用程序或 API 具有在每个租户中创建服务主体，该租户中的用户同意的应用程序使用。 

对于服务主体的凭据可以是通过 Azure 门户生成的密钥或证书。 使用的证书被适合自动化，因为证书被认为更安全与键。 


> [!NOTE]    
> 在 Azure Stack 中使用 AD FS 时，只有管理员可以创建服务主体。 使用 AD FS 时，服务主体需要证书，并需要通过特权终结点 (PEP) 来创建。 有关详细信息，请参阅[提供对 Azure 堆栈的应用程序访问](azure-stack-create-service-principals.md)。

若要了解 Azure Stack 的服务主体，请参阅[创建服务主体](azure-stack-create-service-principals.md)。


### <a name="services"></a>服务
Azure 堆栈中与标识提供程序交互的服务注册为与标识提供程序的应用程序。 如同应用程序，注册可让服务在标识系统中进行身份验证。 

所有 Azure 服务使用[OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code)协议和[JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)以确定其身份。 因为 Azure AD 和 AD FS 上一致地使用协议，你可以使用[Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries)(ADAL) 进行身份验证在本地或 Azure （在连接的情况下）。 使用 ADAL，你可以使用以下方法使用等 Azure PowerShell 和 Azure CLI 工具进行跨云和本地资源管理。 


### <a name="identities-and-your-identity-system"></a>标识和标识系统 
Azure Stack 的标识包括用户帐户、组和服务主体。 

当安装 Azure Stack 时，有多个内置应用程序和服务在目录租户中自动向标识提供者注册。 有些注册的服务用于管理。 还有些服务供用户使用。 默认注册为提供同时与每个其他以及与您在以后添加的标识进行交互的核心服务标识。

如果设置采用多租户的 Azure AD，则有些应用程序会传播到新的目录。 

## <a name="authentication-and-authorization"></a>身份验证和授权
 

### <a name="authentication-by-applications-and-users"></a>应用程序和用户的身份验证
  
![Azure Stack 层之间的标识](media/azure-stack-identity-overview/identity-layers.png)

对应用程序和用户而言，Azure Stack 的体系结构可以用四个层来描述。 各层之间的交互可以使用不同类型的身份验证。


|层    |各层之间的身份验证  |
|---------|---------|
|工具和客户端，如管理门户     | 若要访问或修改 Azure 堆栈中的资源工具和客户端使用[JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)放置到 Azure 资源管理器的调用。 <br>Azure 资源管理器验证 JSON Web 令牌和扫视*声明*在已颁发令牌中估计的级别授权该用户或服务主体具有 Azure 堆栈中。 |
|Azure 资源管理器及其核心服务     |Azure 资源管理器与资源提供程序要传输的用户的通信进行通信。 <br> 传输通过 [Azure 资源管理器模板](/azure/azure-stack/user/azure-stack-arm-templates.md)使用直接命令式调用或声明式调用。|
|资源提供程序     |传递到资源提供程序的调用应受到保护使用基于证书的身份验证。 <br>Azure 资源管理器和资源提供程序则保留在通过 API 进行的通信。 收到来自 Azure 资源管理器每次调用，资源提供程序验证该证书的呼叫。|
|基础结构和业务逻辑     |资源提供程序可与业务逻辑和基础结构通信，通过使用自选的身份验证模式。 Azure 堆栈会随附的默认资源提供程序使用 Windows 身份验证来保护此通信。|

![身份验证所需的信息](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>对 Azure 资源管理器进行身份验证
若要使用的标识提供程序进行身份验证并接收的 JSON Web 令牌，必须具有以下信息： 
1.  **标识系统 （颁发机构） 的 URL**： 可以在其中达到标识提供程序的 URL。 例如，*https://login.windows.net*。 
2.  **应用程序 ID URI 用于 Azure 资源管理器**： 用于 Azure 资源管理器使用标识提供程序注册的唯一标识符。 它也是唯一的每个 Azure 堆栈安装。
3.  **凭据**: 使用的凭据进行身份验证使用标识提供程序。 
4.  **URL 用于 Azure 资源管理器**: URL 是 Azure 资源管理器服务的位置。 例如，  *https://management.azure.com* 或 *https://management.local.azurestack.external* 。

当主体 （客户端、 应用程序或用户） 进行时访问资源的身份验证请求时，请求必须包含：
- 主体的凭据。
- 应用程序 ID URI 主体想要访问的资源。 

凭据已由标识提供者验证。 标识提供程序还将验证应用 ID URI 是对于已注册的应用程序，且主体拥有正确的权限，以获取该资源的令牌。 如果请求有效，则授予 JSON Web 令牌。 

令牌必须传递请求的标头中对 Azure 资源管理器。 Azure 资源管理器将执行以下操作，以非特定的顺序操作：
- 验证*颁发者*(iss) 声明，以确认令牌是来自正确的标识提供程序。 
- 验证*受众*(aud) 声明，以确认令牌的颁发到 Azure 资源管理器。 
- 验证的 JSON Web 令牌进行签名使用 OpenID 通过配置的证书已知到 Azure 资源管理器。 
- 查看*颁发时间*(iat) 和*过期*(exp) 声明以确认令牌处于活动状态，以及可以接受。 

Azure 资源管理器完成所有验证后，使用*对象*(oid) 和*组*声明以使主体可以访问的资源的列表。 

![令牌交换协议示意图](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>使用基于角色的访问控制  
基于角色的访问控制 (RBAC) 在 Azure 堆栈在与 Microsoft Azure 中实现一致。 可以通过将相应的 RBAC 角色分配给用户、组和应用程序，来管理资源访问权限。 有关如何使用 Azure 堆栈的 RBAC 的信息，请参阅以下文章：
- [Azure 门户中基于角色的访问控制入门](/azure/active-directory/role-based-access-control-what-is)。
- [使用基于角色的访问控制管理 Azure 订阅资源的访问权限](/azure/active-directory/role-based-access-control-configure)
- [创建用于 Azure 基于角色的访问控制的自定义角色](/azure/active-directory/role-based-access-control-custom-roles)
- 在 Azure Stack 中[管理基于角色的访问控制](azure-stack-manage-permissions.md)。


### <a name="authenticate-with-azure-powershell"></a>使用 Azure PowerShell 进行身份验证  
有关使用 Azure PowerShell 在 Azure Stack 中进行身份验证的详细信息，请参阅[配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

### <a name="authenticate-with-azure-cli"></a>使用 Azure CLI 进行身份验证
有关使用 Azure PowerShell 与 Azure 堆栈进行身份验证的信息，请参阅[安装和配置 Azure CLI 用于 Azure 堆栈](/azure/azure-stack/user/azure-stack-connect-cli.md)。

## <a name="next-steps"></a>后续步骤
- [标识体系结构](azure-stack-identity-architecture.md)   
- [数据中心集成的标识](azure-stack-integrate-identity.md)




