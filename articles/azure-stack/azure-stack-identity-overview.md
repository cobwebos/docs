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
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>标识 Azure 堆栈的概述

Azure 堆栈需要 Azure AD 或 Active Directory 联合服务 (AD FS) 支持通过 Active Directory (AD) 为标识提供程序。 以下概念和授权详细信息可以帮助您做出的选择标识提供程序之间。 提供程序的选择是一个一次性的决定，请首先部署 Azure 堆栈时。  

你选择 Azure AD 和 AD FS 可能会受到在其中部署 Azure 堆栈的模式： 
- 在部署在连接模式下时，你可以使用 Azure AD 或 AD FS。 
- 当你部署在未连接到 Internet 断开连接模式下时，支持仅 AD FS。

有关这些选项的详细信息，请查看具体取决于你的 Azure 堆栈环境的以下文章：
- Azure 堆栈部署工具包，请参阅[标识注意事项](azure-stack-datacenter-integration.md#identity-considerations)。
- Azure 堆栈集成系统，请参阅[部署规划 Azure 堆栈的决策集成系统](azure-stack-deployment-decisions.md)。

 
## <a name="common-concepts-for-identity"></a>标识的基本概念
下列各节讨论了标识提供程序和 Azure 堆栈中的使用它们的基本概念。
![标识提供程序的术语](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>目录租户和组织
目录是保存有关的信息的容器*用户*，*应用程序*，*组*，和*服务主体*。
 
目录租户是*组织*，如 Microsoft 或你自己的公司。 
- Azure AD 支持多个租户并且可支持多个组织，他们自己的目录中的每个。 如果您使用 Azure AD，并且有多个租户，您可以授予应用程序和从一个租户访问权限向这同一个目录的其他租户的用户。
- AD FS 支持仅单租户，因此单个组织。 

### <a name="users-and-groups"></a>用户和组
用户帐户 （标识） 是指对个人使用用户 ID 和密码进行身份验证的标准帐户。 组可以包括用户或其他组。 

如何创建和管理用户和组取决于你使用的标识解决方案。 

在 Azure 堆栈，用户帐户： 
- 在中创建 *username@domain* 格式。 虽然 AD FS 映射到的 Active Directory 用户帐户，但 AD FS 不支持使用_&lt;域 >\<别名 >_格式。 
- 可以设置为使用多因素身份验证。 
- 仅限于在其中它们首先注册，这是其组织目录的目录。
- 可以从本地目录导入。 有关详细信息，请参阅[将你的本地目录与 Azure Active Directory 集成](/azure/active-directory/connect/active-directory-aadconnect)Azure 文档中。  

当你登录到你组织租户门户时，你将使用 https://portal.local.azurestack.external。 

### <a name="guest-users"></a>来宾用户
来宾用户是从已被授予对你的目录中的资源的访问其他目录租户的用户帐户。 若要支持来宾用户，必须使用 Azure AD，并启用对多租户支持。 支持时，你可以在你的目录租户中，这样与外部组织的协作邀请来宾用户访问资源。 

若要邀请来宾用户，云操作员和用户可以使用[Azure AD B2B 协作](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。 邀请到文档、 资源和应用程序从你的目录的用户获取访问时保持对你自己的资源和数据控制。 

作为来宾用户，你可以登录到另一个组织的目录租户。 为此，必须将该组织的目录名追加到门户 URL。  例如如果你属于 contoso.com，但想要登录到 Fabrikam 目录，则使用 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com。

### <a name="applications"></a>应用程序
你可以注册到 Azure AD 或 AD FS，应用程序，然后将它们在你的组织提供给用户。 

应用程序包括：
- **Web 应用程序**– 示例包括 Azure 门户和 Azure 资源管理器。  它们支持 Web API 调用。 
- **本机客户端**– 示例包括 Azure PowerShell、 Visual Studio 和 Azure 命令行界面 (CLI)。

应用程序可以支持两种类型的租户： 
- **单租户**应用程序支持用户和服务只能从注册该应用程序的相同目录。 

  > [!NOTE]    
  > 由于 AD FS 支持单个目录，你的 AD FS 拓扑中创建的应用程序是设计使然，单租户应用程序。
- **多租户**用户和服务从这两个目录中注册该应用程序，然后从其他租户目录的应用程序支持使用。  与多租户应用程序的另一个租户目录 （另一个 Azure AD 租户） 可以在用户登录到你的应用程序。     

  有关多租户的详细信息，请参阅[启用多租户](azure-stack-enable-multitenancy.md)。  

  有关开发多租户应用程序的详细信息，请参阅[多租户应用](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)。

注册应用程序时，将创建两个对象：
- **应用程序对象**– 应用程序对象是跨所有租户的全局表示形式的应用程序。 此关系是一对一的软件应用程序，仅在其中应用程序是第一个寄存器的目录中存在。

 
   
- **服务主体对象**– 服务主体是为第一次注册该应用程序的目录中的应用程序创建的凭据。 也在每个将使用该应用程序的其他租户的目录中创建服务主体。 此关系可以是一个一对多的软件应用程序。   

若要了解有关应用程序和服务主体对象的详细信息，请参阅[应用程序和 Azure Active Directory (Azure AD) 中的服务主体对象](/azure/active-directory/develop/active-directory-application-objects)。 

### <a name="service-principals"></a>服务主体 
服务主体是一套*凭据*为应用程序或服务授予对 Azure 堆栈中的资源的访问。 使用服务主体分隔从使用应用程序的用户的权限的应用程序权限。

在其中使用的应用程序的每个租户中创建服务主体。 服务主体建立用于登录和访问资源 （如用户） 保护的该租户的标识。   

- 单租户应用程序在其第一次创建时所在的目录中有只有一个服务主体。  此服务主体将创建并表示同意后，若要在应用程序的注册过程中使用。 
- 多租户 Web 应用程序或 API 具有服务主体在每个租户中创建该租户中的用户使用此应用程序许可了其中。  

对于服务主体的凭据可以是 （通过 Azure 门户生成） 的密钥或证书。  使用的证书应适合自动化，因为证书被视为比使用密钥更安全。 


> [!NOTE]    
> 当使用 AD FS 与 Azure 堆栈上时，只有管理员可以创建服务主体。 使用 AD FS 时，服务主体需要证书，并通过特权终结点 (PEP) 进行创建。 有关详细信息，请参阅，[提供对 Azure 堆栈的应用程序访问](azure-stack-create-service-principals.md)。

若要了解有关 Azure 堆栈的服务主体的信息，请参阅[创建服务主体](azure-stack-create-service-principals.md)。


### <a name="services"></a>服务
Azure 堆栈中的服务进行交互与标识提供程序，注册为与标识提供程序的应用程序。 类似于应用程序，注册可以让服务使用的标识系统进行身份验证。 

所有 Azure 服务使用[OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code)协议和[JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)(JWT)，以确定其身份。 由于通过 Azure AD 使用一致的协议和 AD FS，你可以使用 Azure [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 进行身份验证在本地或 Azure （在连接的情况下）。 ADAL 还使你能够跨云使用 Azure PowerShell 和 CLI 之类的工具和本地资源管理。 


### <a name="identities-and-your-identity-system"></a>标识和标识系统 
Azure 堆栈的标识包括用户帐户、 组和服务主体。 在安装 Azure 堆栈时，几个内置的应用程序和服务自动注册到标识提供者在目录租户。 用于管理的注册某些服务。 还为用户提供其他服务。 默认注册为提供核心服务标识可以彼此进行交互并与标识您以后添加。
如果你设置 Azure AD 的多租户时，某些应用程序会传播到新目录。  

## <a name="authentication-and-authorization"></a>身份验证和授权
 

### <a name="authentication-by-applications-and-users"></a>通过应用程序和用户的身份验证
  
![Azure 堆栈的各层之间的标识](media/azure-stack-identity-overview/identity-layers.png)

对于应用程序和用户，由四个层描述 Azure 堆栈的体系结构。 每个层之间的交互可以使用不同类型的身份验证。


|层    |各层之间的身份验证  |
|---------|---------|
|工具和客户端，如管理门户     | 若要访问或修改 Azure 堆栈中的资源工具和客户端使用[JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)(JWT) 将对 Azure 资源管理的调用。  <br><br> Azure 资源管理器验证 JWT 和扫视*声明*在已颁发令牌中估计的级别授权该用户或服务主体具有 Azure 堆栈中。        |
|Azure 资源管理器和其核心服务     |Azure 资源管理器与资源提供程序要传输的用户的通信进行通信。 <br><br> 传输使用*直接命令性*调用或*声明性*通过调用[Azure 资源管理器模板](/azure/azure-stack/user/azure-stack-arm-templates.md)。|
|资源提供程序     |传递到资源提供程序的调用应受到保护使用基于证书的身份验证。 <br><br>Azure 资源管理器和资源提供程序则继续保留在通过 API 进行的通信。 收到来自 Azure 资源管理器每次调用，资源提供程序验证该证书的呼叫。|
|基础结构和业务逻辑     |资源提供程序可与业务逻辑和基础结构通信，通过使用自选的身份验证模式。 默认的资源提供程序提供与 Azure 堆栈使用 Windows 身份验证来保护此通信。|

![所需的身份验证信息](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>身份验证对 Azure 资源管理器
若要使用的标识提供程序进行身份验证并接收一个 JWT，必须具有以下信息： 
1.  **标识系统 （颁发机构） 的 URL** – 可以从该处达到标识提供程序的 URL。 例如，  *&lt;https://login.windows.net>*。 
2.  **应用程序 ID URI 用于 Azure 资源管理器**– 为了使 Azure 资源管理器是已向标识提供程序，注册的每个 Azure 堆栈安装所特有的唯一标识符。
3.  **凭据**– 此正在使用与标识提供程序进行身份验证的凭据。  
4.  **URL 用于 Azure 资源管理器**– URL 是 Azure 资源管理器服务的位置。 例如，  *&lt;https://management.azure.com>*或 *&lt;https://management.local.azurestack.external>*。

当主体 （客户端、 应用程序或用户） 进行身份验证请求访问资源时，该请求必须包含：
- 主体的凭据。
- 他们想要访问的资源应用程序 ID URI。  

标识提供程序验证的凭据。 标识提供程序还将验证对于已注册的应用程序中，验证应用程序 ID URI，以及主体拥有正确的权限，以获取用于该资源的令牌。 如果该请求是有效的将授予的 JSON Web 令牌。 

令牌必须传递请求的标头中对 Azure 资源管理器。 Azure 资源管理器将执行以下验证以非特定的顺序操作：
- 验证*颁发者*(iss) 声明，以确认令牌是来自正确的标识提供程序。 
- 验证*受众*(aud) 声明，以确认已颁发令牌的 Azure 资源管理器。 
- 验证 JWT 使用通过 OpenID、 配置和已知到 Azure 资源管理器证书签名。 
- 查看*颁发时间*(iat) 和*到期*(exp) 声明以确认令牌处于活动状态，以及可以接受。 

Azure 资源管理器完成所有验证后，使用*对象*(oid) 和*组*声明以使主体可以访问的资源的列表。 

![令牌交换协议的图示](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>使用基于角色的访问控制  
基于角色的访问控制 (RBAC) 在 Azure 堆栈在与 Microsoft Azure 中实现一致。  你可以通过将相应的 RBAC 角色分配给用户、 组和应用程序来管理对资源的访问。 有关如何使用 Azure 堆栈的 RBAC 的详细说明，请查看以下文章：
- [要开始使用 Azure 门户中基于角色的访问控制](/azure/active-directory/role-based-access-control-what-is)。
- [使用基于角色的访问控制来管理对你的 Azure 订阅的资源的访问](/azure/active-directory/role-based-access-control-configure)。
- [为 azure 基于角色的访问控制中创建自定义角色](/azure/active-directory/role-based-access-control-custom-roles)。
- [管理基于角色的访问控制](azure-stack-manage-permissions.md)Azure 堆栈中。


### <a name="authenticate-with-azure-powershell"></a>使用 Azure PowerShell 进行身份验证  
有关使用 Azure PowerShell 与 Azure 堆栈进行身份验证的详细信息，请参阅[配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)。

### <a name="authenticate-with-azure-cli"></a>使用 Azure CLI 进行身份验证
有关使用 Azure PowerShell 与 Azure 堆栈进行身份验证的详细信息，请参阅[安装和配置 CLI 用于 Azure 堆栈](/azure/azure-stack/user/azure-stack-connect-cli.md)。

## <a name="next-steps"></a>后续步骤
- [标识体系结构](azure-stack-identity-architecture.md)   
- [数据中心集成的标识](azure-stack-integrate-identity.md)




