---
title: 使用国家/地区云中的 Microsoft 身份验证库（MSAL）-Microsoft 标识平台
description: Microsoft 身份验证库（MSAL）使应用程序开发人员能够获取令牌，以便调用受保护的 web Api。 这些 web Api 可以是 Microsoft Graph、其他 Microsoft Api、合作伙伴 web Api 或你自己的 web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f1c73d89b0efc17e8f8836d080595927d500ad6
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481863"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在国家/地区云环境中使用 MSAL

[国内云](authentication-national-cloud.md)（也称为主权云）是 Azure 的物理上独立的实例。 Azure 的这些区域可帮助确保数据驻留、主权和合规性要求在地理边界内有效。

除了 Microsoft 全球云以外，Microsoft 身份验证库（MSAL）还使国家/地区的应用程序开发人员能够获取令牌，以便进行身份验证和调用受保护的 web Api。 这些 web Api 可以 Microsoft Graph 或其他 Microsoft Api。

在以下国家/地区中部署 Azure Active Directory （Azure AD）包括全局云：  

- Azure 政府
- Azure 中国世纪互联
- Azure Germany

本指南演示如何登录到工作和学校帐户，获取访问令牌，并在[Azure 政府版云](https://azure.microsoft.com/global-infrastructure/government/)环境中调用 Microsoft Graph API。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保满足以下先决条件。

### <a name="choose-the-appropriate-identities"></a>选择适当的标识

[Azure 政府](https://docs.microsoft.com/azure/azure-government/)版应用程序可使用 Azure AD 政府标识，Azure AD 公共标识对用户进行身份验证。 由于可以使用任何这些标识，因此需要决定应为方案选择哪个颁发机构终结点：

- Azure AD 公有：如果你的组织已有一个 Azure AD 公有租户来支持 Office 365 （公有或 GCC）或其他应用程序，则通常使用。
- Azure AD 政府：如果你的组织已有一个 Azure AD 政府租户支持 Office 365 （GCC 高版或 DoD），或在 Azure AD 政府版中创建新租户，则通常使用此项。

确定之后，在执行应用注册的位置需要特别注意。 如果为 Azure 政府版应用程序选择 Azure AD 公共标识，则必须在 Azure AD 公有租户中注册该应用程序。

### <a name="get-an-azure-government-subscription"></a>获取 Azure 政府版订阅

若要获取 Azure 政府版订阅，请参阅[在 Azure 政府版中管理和连接到你的订阅](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。

如果你没有 Azure 政府版订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/global-infrastructure/government/request/)。

若要详细了解如何使用特定编程语言的国家/地区云，请选择与你的语言相匹配的选项卡：

## <a name="nettabdonet"></a>[.NET](#tab/donet)

可以使用 MSAL.NET 登录用户、获取令牌并在国家/地区云中调用 Microsoft Graph API。

以下教程演示了如何生成 .NET Core 2.2 MVC Web 应用。 此应用使用 OpenID Connect 通过属于全国云的组织中的工作和学校帐户来登录用户。

- 若要登录用户和获取令牌，请按照本教程操作：在[主权云中使用 Microsoft 标识平台构建 ASP.NET Core Web 应用登录用户](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 若要调用 Microsoft Graph API，请按照本教程操作：[使用 microsoft 标识平台，代表用户使用其在 Microsoft 全国云中的工作和学校帐户登录来调用 Microsoft Graph ASP.NET CORE API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

为主权云启用 MSAL 应用程序：

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

1. 登录到 [Azure 门户](https://portal.azure.us/)。
    
   若要查找其他国家云 Azure 门户终结点，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果你的帐户允许你访问多个租户，请在右上角选择你的帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 请参阅面向开发人员的 Microsoft 标识平台上的 "[应用注册](https://aka.ms/ra/ff)" 页。
1. “注册应用程序”页显示后，请输入应用程序的名称。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。
1. 在 "**重定向 URI** " 部分中，选择**web**平台，并基于你的 Web 服务器将值设置为应用程序的 URL。 有关如何在 Visual Studio 和节点中设置和获取重定向 URL 的说明，请参阅后续部分。
1. 选择“注册”。
1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值。
1. 本教程要求你启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左窗格中，选择“身份验证”。
1. 在“高级设置”部分的“隐式授权”下，选中“ID 令牌”和“访问令牌”复选框。 ID 令牌和访问令牌是必需的，因为此应用需要登录用户并调用 API。
1. 选择“保存”。

### <a name="step-2--set-up-your-web-server-or-project"></a>步骤2：设置 web 服务器或项目

- 下载本地 web 服务器（如 Node）的[项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。

  或

- [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然后，在运行[JAVASCRIPT SPA](#step-4-configure-your-javascript-spa)之前，将其配置为配置代码示例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>步骤3：使用 Microsoft 身份验证库登录用户

按照[JavaScript 教程](tutorial-v2-javascript-spa.md#create-your-project)中的步骤创建项目，并与 MSAL 集成以登录用户。

### <a name="step-4-configure-your-javascript-spa"></a>步骤4：配置 JavaScript SPA

在设置项目期间创建的 `index.html` 文件中，添加应用程序注册信息。 在 `<script></script>` 文件正文的 `index.html` 标记顶部添加以下代码：

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

在该代码中：

- `Enter_the_Application_Id_here` 是你注册的应用程序的**应用程序（客户端） ID**值。
- `Enter_the_Tenant_Info_Here` 设置为以下选项之一：
    - 如果你的应用程序支持**此组织目录中的帐户**，请将此值替换为租户 ID 或租户名称（例如，contoso.microsoft.com）。
    - 如果你的应用程序支持**任何组织目录中的帐户**，请将此值替换 `organizations`。
    
    若要查找所有国家/地区云的身份验证终结点，请参阅[Azure AD 身份验证终结点](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 国内云不支持个人 Microsoft 帐户。
  
- `graphEndpoint` 是适用于美国政府的 Microsoft 云的 Microsoft Graph 终结点。

   若要查找所有国家云 Microsoft Graph 终结点，请参阅[国家/地区云中的 Microsoft Graph 终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="pythontabpython"></a>[Python](#tab/python)

为主权云启用 MSAL Python 应用程序：

- 在特定门户中注册你的应用程序，具体取决于云。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 使用存储库中的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)，对配置进行一些更改，具体取决于云，接下来会提到。
- 根据你在其中注册应用程序的云，使用特定权限。 有关不同云的颁发机构的详细信息，请参阅[Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

    下面是一个示例证书颁发机构：

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- 若要调用 Microsoft graph，需要一个特定的图形终结点 URL，该 URL 依赖于所使用的云。 若要查找所有国家/地区云 Microsoft Graph 终结点，请参阅[Microsoft Graph 和 Graph 资源管理器服务根终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

    下面是一个具有作用域的图形终结点示例：
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="javatabjava"></a>[Java](#tab/java)

若要为主权云启用 MSAL for Java 应用程序，请执行以下操作：

- 在特定门户中注册你的应用程序，具体取决于云。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 使用存储库中的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)，对配置进行一些更改，具体取决于云，具体如下所述。
- 根据你在其中注册应用程序的云，使用特定权限。 有关不同云的颁发机构的详细信息，请参阅[Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

下面是一个示例证书颁发机构：

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- 若要调用 Microsoft graph，需要一个特定的图形终结点 URL，该 URL 依赖于所使用的云。 若要查找所有国家/地区云 Microsoft Graph 终结点，请参阅[Microsoft Graph 和 Graph 资源管理器服务根终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

下面是一个具有作用域的图形终结点示例：

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

适用于 iOS 和 macOS 的 MSAL 可用于在国家/地区云中获取令牌，但在创建 `MSALPublicClientApplication`时需要进行额外配置。

例如，如果希望应用程序成为国家/地区云（此处为美国政府）的多租户应用程序，可以编写：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swifttabswift"></a>[Swift](#tab/swift)

适用于 iOS 和 macOS 的 MSAL 可用于在国家/地区云中获取令牌，但在创建 `MSALPublicClientApplication`时需要进行额外配置。

例如，如果希望应用程序成为国家/地区云（此处为美国政府）的多租户应用程序，可以编写：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [国家/地区云中的身份验证](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)
