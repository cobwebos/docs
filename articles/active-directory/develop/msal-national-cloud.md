---
title: 在国家云应用中使用 MSAL |蔚蓝
titleSuffix: Microsoft identity platform
description: Microsoft 身份验证库 （MSAL） 使应用程序开发人员能够获取令牌，以便调用安全的 Web API。 这些 Web API 可以是 Microsoft 图形、其他 Microsoft API、合作伙伴 Web API 或您自己的 Web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533983"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在国家云环境中使用 MSAL

[国家云](authentication-national-cloud.md)（也称为主权云）是 Azure 的物理隔离实例。 Azure 的这些区域有助于确保数据驻留、主权和合规性要求在地理范围内得到遵守。

除了 Microsoft 全球云之外，Microsoft 身份验证库 （MSAL） 还允许国家云中的应用程序开发人员获取令牌，以便对安全的 Web API 进行身份验证和调用。 这些 Web API 可以是 Microsoft 图形或其他 Microsoft API。

包括全局云，Azure 活动目录 （Azure AD） 部署在以下国家云中：  

- Azure Government
- Azure 中国世纪互联
- Azure 德国

本指南演示如何登录到工作帐户和学校帐户、获取访问令牌以及在[Azure 政府云](https://azure.microsoft.com/global-infrastructure/government/)环境中调用 Microsoft 图形 API。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保满足这些先决条件。

### <a name="choose-the-appropriate-identities"></a>选择适当的标识

[Azure 政府](https://docs.microsoft.com/azure/azure-government/)应用程序可以使用 Azure AD 政府标识和 Azure AD 公共标识对用户进行身份验证。 由于可以使用这些标识中的任何一个，因此需要决定应为方案选择哪个权限终结点：

- Azure AD 公共：如果组织已具有 Azure AD 公共租户以支持 Office 365（公共或 GCC）或其他应用程序，则通常使用。
- Azure AD 政府：如果组织已具有 Azure AD 政府租户以支持 Office 365（GCC High 或 DoD）或在 Azure AD 政府中创建新租户，则常用。

在您做出决定后，一个特别的考虑因素是执行应用注册的位置。 如果为 Azure 政府应用程序选择 Azure AD 公共标识，则必须在 Azure AD 公共租户中注册该应用程序。

### <a name="get-an-azure-government-subscription"></a>获取 Azure 政府订阅

要获取 Azure 政府订阅，请参阅[在 Azure 政府 中管理和连接到订阅](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。

如果没有 Azure 政府订阅，请先创建[一个免费帐户](https://azure.microsoft.com/global-infrastructure/government/request/)。"

有关使用具有特定编程语言的国家云的详细信息，请选择与您的语言匹配的选项卡：

## <a name="net"></a>[.NET](#tab/donet)

您可以使用MSAL.NET登录用户、获取令牌并在国家云中调用 Microsoft 图形 API。

以下教程演示如何构建 .NET 核心 2.2 MVC Web 应用。 该应用程序使用 OpenID Connect 在属于国家云的组织中使用工作帐户和学校帐户登录用户。

- 要登录用户并获取令牌，请按照本教程操作：[使用 Microsoft 标识平台在主权云中构建ASP.NET核心 Web 应用登录用户](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 要调用 Microsoft 图形 API，请按照本教程操作：[使用 Microsoft 标识平台从ASP.NET Core 2.x Web 应用调用 Microsoft 图形 API，代表用户使用他们的工作和学校帐户在 Microsoft 国家云中登录](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="javascript"></a>[JavaScript](#tab/javascript)

要启用主权云的 MSAL.js 应用程序，请执行：

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

1. 登录 [Azure 门户](https://portal.azure.us/)。

   要查找其他国家云的 Azure 门户终结点，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果您的帐户允许您访问多个租户，请在右上角选择您的帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 转到 Microsoft 标识平台上面向开发人员[的应用注册](https://aka.ms/ra/ff)页面。
1. “注册应用程序”页显示后，请输入应用程序的名称。 
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。********
1. 在 **"重定向 URI"** 部分中，选择**Web**平台并根据 Web 服务器将值设置为应用程序的 URL。 有关如何在 Visual Studio 和 Node 中设置和获取重定向 URL 的说明，请参阅以下部分。
1. 选择“注册”  。
1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值。********
1. 本教程要求您启用[隐式授予流](v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左窗格中，选择“身份验证”  。
1. 在“高级设置”部分的“隐式授权”下，选中“ID 令牌”和“访问令牌”复选框     。 需要 ID 令牌和访问令牌，因为此应用需要登录用户并调用 API。
1. 选择“保存”。 

### <a name="step-2--set-up-your-web-server-or-project"></a>第 2 步：设置 Web 服务器或项目

- 下载本地 Web 服务器（如 Node）[的项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。

  or

- [下载视觉工作室项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然后跳到[配置 JavaScript SPA](#step-4-configure-your-javascript-spa)以在运行代码示例之前配置代码示例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>第 3 步：使用 Microsoft 身份验证库登录用户

按照[JavaScript 教程](tutorial-v2-javascript-spa.md#create-your-project)中的步骤创建项目并与 MSAL 集成以登录用户。

### <a name="step-4-configure-your-javascript-spa"></a>第 4 步：配置 JavaScript SPA

在设置项目期间创建的 `index.html` 文件中，添加应用程序注册信息。 在 `index.html` 文件正文中的 `<script></script>` 标记顶部添加以下代码：

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

- `Enter_the_Application_Id_here`是您注册的应用程序**的应用程序（客户端）ID**值。
- `Enter_the_Tenant_Info_Here`设置为以下选项之一：
    - 如果应用程序支持**此组织目录中的帐户**，请将此值替换为租户 ID 或租户名称（例如，contoso.microsoft.com）。
    - 如果应用程序支持**任何组织目录中的帐户**，请将此值替换为`organizations`。

    要查找所有国家云的身份验证终结点，请参阅 Azure [AD 身份验证终结点](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 国家云不支持个人 Microsoft 帐户。

- `graphEndpoint`是美国政府的微软云的微软图形终结点。

   要查找所有国家云的 Microsoft 图形终结点，请参阅[国家云中的 Microsoft 图形终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="python"></a>[Python](#tab/python)

要为主权云启用 MSAL Python 应用程序，请执行：

- 根据云，请在特定门户中注册应用程序。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 根据接下来提到的云，请使用来自回购的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)对配置进行一些更改。
- 使用特定权限，具体取决于您在其中注册应用程序的云。 有关不同云的颁发权限的详细信息，请参阅[Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

    下面是一个示例权限：

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- 要调用 Microsoft 图形需要特定的图形终结点 URL，具体取决于您使用的云。 要查找所有国家云的 Microsoft 图形终结点，请参阅[Microsoft 图形和图形资源管理器服务根终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

    下面是具有作用域的图形终结点的示例：

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

要为主权云启用 JAVA 应用程序的 MSAL，请执行：

- 根据云，请在特定门户中注册应用程序。 有关如何选择门户的详细信息，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)
- 根据接下来提到的云，使用来自回购的任何[示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)对配置进行一些更改。
- 使用特定权限，具体取决于您在其中注册应用程序的云。 有关不同云的颁发权限的详细信息，请参阅[Azure AD 身份验证终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)。

下面是一个示例权限：

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- 要调用 Microsoft 图形需要特定的图形终结点 URL，具体取决于您使用的云。 要查找所有国家云的 Microsoft 图形终结点，请参阅[Microsoft 图形和图形资源管理器服务根终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

下面是具有作用域的图形终结点的示例：

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

适用于 iOS 和 macOS 的 MSAL 可用于在国家云中获取令牌，但在创建`MSALPublicClientApplication`时需要额外的配置。

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

## <a name="swift"></a>[Swift](#tab/swift)

适用于 iOS 和 macOS 的 MSAL 可用于在国家云中获取令牌，但在创建`MSALPublicClientApplication`时需要额外的配置。

例如，如果希望应用程序成为国家/地区云（此处为美国政府）的多租户应用程序，可以编写：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [国家云中的身份验证](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)
