---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322596"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>访问令牌

由[授权服务器](#authorization-server)颁发的一种[安全令牌](#security-token)，可供[客户端应用程序](#client-application)用来访问[受保护的资源服务器](#resource-server)。 Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. 该令牌包含所有适用的主体相关[声明](#claim)，可让客户端应用程序将其作为某种形式的凭据来访问给定的资源。 并使得资源所有者不必对客户端公开凭据。

根据提供的凭据，访问令牌有时称为“用户+应用”或“仅限应用”。 例如，如果客户端应用程序：

* 使用[“授权代码”授权](#authorization-grant)，则最终用户先以资源所有者的身份进行身份验证，将授权委托给客户端以访问资源。 然后，客户端在获取访问令牌时进行身份验证。 令牌有时可以更具体地称为“用户+应用”令牌，因为它同时代表授权客户端应用程序的用户，以及应用程序。
* 使用[“客户端凭据”授权](#authorization-grant)，客户端将提供唯一身份验证，在没有资源所有者的身份验证/授权情况下正常运行，因此该令牌有时可称为“仅限应用”令牌。

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

Azure AD 向应用程序注册颁发的唯一标识符，用于标识特定应用程序及相关联的配置。 This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>应用程序清单

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>应用程序对象

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. 应用程序对象可全局（在其能够访问的所有租户中）定义应用程序的标识配置，并可提供模板来派生出其对应的服务主体对象，在运行时于本地（在特定租户中）使用。

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>应用程序注册

要允许某个应用程序与标识和访问管理功能集成并将这些功能委托给 Azure AD，必须向 Azure AD [租户](#tenant)注册该应用程序。 向 Azure AD 注册应用程序时，必须提供应用程序的标识配置，以允许它与 Azure AD 集成并使用如下所述的功能：

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [同意框架](#consent)用于根据资源所有者授权来管理客户端对受保护资源的访问。

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>authentication

向访问方质询合法凭据的措施，提供创建用于标识和访问控制的安全主体的基础。 例如，在 [OAuth2 授权](#authorization-grant)期间，访问方身份验证根据使用的授权填充[资源所有者](#resource-owner)或[客户端应用程序](#client-application)角色。

## <a name="authorization"></a>authorization

授权经过身份验证的安全主体执行某项操作的措施。 在 Azure AD 编程模型中有两个主要用例：

* 在 [OAuth2 授权](#authorization-grant)流程中：[资源所有者](#resource-owner)向[客户端应用程序](#client-application)授权时，允许客户端访问资源所有者的资源。
* 在客户端访问资源期间：与[资源服务器](#resource-server)实现的机制一样，使用[访问令牌](#access-token)中提供的[声明](#claim)值作为依据做出访问控制决策。

## <a name="authorization-code"></a>授权代码

在四个 OAuth2 [授权](#authorization-grant)之一的“授权代码”流程中，由[授权终结点](#authorization-endpoint)提供给[客户端应用程序](#client-application)的短期“令牌”。 为响应[资源所有者](#resource-owner)的身份验证，将授权代码返回给客户端应用程序，指出资源所有者已进行所请求资源的访问授权委托。 在执行流程的过程中，稍后会将授权代码兑换为[访问令牌](#access-token)。

## <a name="authorization-endpoint"></a>授权终结点

[授权服务器](#authorization-server)实现的终结点之一，用来与[资源所有者](#resource-owner)进行交互，以便在 OAuth2 授权流程期间提供[授权](#authorization-grant)。 根据使用的授权流程，实际提供的授权可能不同，这包括[授权代码](#authorization-code)或[安全令牌](#security-token)。

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>授权

授予[客户端应用程序](#client-application)的凭据，代表[资源所有者](#resource-owner)对其受保护资源访问权限的[授权](#authorization)。 A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". 根据使用的授权类型，返回给客户端的凭据是[访问令牌](#access-token)或[授权代码](#authorization-code)（稍后用于交换访问令牌）。

## <a name="authorization-server"></a>授权服务器

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. [客户端应用程序](#client-application)在运行时根据 OAuth2 定义的[权限授予](#authorization-grant)，通过其[权限](#authorization-endpoint)和[令牌](#token-endpoint)终结点来与授权服务器交互。

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>声明

[安全令牌](#security-token)包含声明，声明将有关某个实体（例如[客户端应用程序](#client-application)或[资源所有者](#resource-owner)）的断言提供给另一个实体（例如[资源服务器](#resource-server)）。 声明是中继令牌主体（例如，由[授权服务器](#authorization-server)进行身份验证的安全主体）相关事实的名称/值对。 给定令牌中的声明依赖于几个变量，包括令牌类型、用于验证主体身份的凭据类型和应用程序配置等。

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>客户端应用程序

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). “客户端”一词并不代表任何特定的硬件实现特征（例如，应用程序是在服务器、台式机还是其他设备上执行）。

客户端应用程序向资源所有者请求[授权](#authorization)，以参与 [OAuth2 授权](#authorization-grant)流程，并可代表资源所有者访问 API/数据。 The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. 应用程序可实现在 Web 服务器上运行的 [Web 客户端（机密）](#web-client)、安装在设备上的[本机客户端（公共）](#native-client)，或者在设备浏览器中运行的[基于用户代理的客户端（公共）](#user-agent-based-client)。

## <a name="consent"></a>同意

[资源所有者](#resource-owner)授权给[客户端应用程序](#client-application)，让其通过特定[权限](#permissions)代表资源所有者访问受保护资源的过程。 根据客户端请求的权限，要求管理员或用户同意分别允许其组织/个人数据的访问权限。 请注意，在[多租户](#multi-tenant-application)方案中，应用程序的[服务主体](#service-principal-object)也会记录在同意方用户的租户中。

有关详细信息，请参阅[许可框架](consent-framework.md)。

## <a name="id-token"></a>ID 令牌

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. 不过，与访问令牌不同的是，ID 令牌的声明并不用于与资源访问相关的用途（具体地说，是访问控制）。

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Microsoft 标识平台

Microsoft 标识平台是 Azure Active Directory (Azure AD) 标识服务和开发人员平台的一种演变。 开发人员可以通过它来生成应用程序，以便进行所有 Microsoft 标识的登录，以及获取令牌来调用 Microsoft Graph、其他 Microsoft API 或者开发人员生成的 API。 它是一种全功能的平台，包含身份验证服务、库、应用程序注册和配置、完整的开发人员文档、代码示例，以及其他开发人员内容。 Microsoft 标识平台支持行业标准协议，例如 OAuth 2.0 和 OpenID Connect。 有关更多详细信息，请参阅[关于 Microsoft 标识平台](about-microsoft-identity-platform.md)。

## <a name="multi-tenant-application"></a>多租户应用程序

一类应用程序，允许在任何 Azure AD [租户](#tenant)（包括在其中注册了客户端的租户以外的租户）中预配的用户进行登录和[同意](#consent)操作。 [本机客户端](#native-client)应用程序默认为多租户，而 [Web 客户端](#web-client)和 [Web 资源/API](#resource-server) 应用程序则可在单租户和多租户之间做出选择。 相反，注册为单租户的 Web 应用程序只允许来自应用程序注册所在相同租户中预配的用户帐户的登录。

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>本机客户端

设备上本机安装的[客户端应用程序](#client-application)类型。 由于所有代码都在设备上执行，因此设备因为无法隐私/秘密地存储凭据而被视为“公共”客户端。 See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>权限

[客户端应用程序](#client-application)通过声明权限请求来获取[资源服务器](#resource-server)访问权限。 有两种权限类型：

* “委托的”权限，可使用登录的[资源所有者](#resource-owner)的委托授权指定[基于范围](#scopes)的访问，在运行时提供给资源作为客户端[访问令牌](#access-token)中的[“scp”声明](#claim)。
* “应用程序”权限，可使用客户端应用程序的凭据/标识指定[基于角色](#roles)的访问，在运行时提供给资源作为客户端访问令牌中的[“角色”声明](#claim)。

权限也会在[同意](#consent)过程中出现，让管理员或资源所有者有机会允许/拒绝客户端对其租户中的资源进行访问。

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). [公共客户端](#client-application)无法安全地维护凭据，因此它只能请求委托的权限，而[机密客户端](#client-application)则可以请求委托的权限和应用程序权限。 The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>资源所有者

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. 如果资源所有者是个人，则称为最终用户。 For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>资源服务器

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). 它也称为受保护的资源服务器或资源应用程序。

资源服务器使用 OAuth 2.0 授权框架公开 API，并通过[范围](#scopes)和[角色](#roles)强制实施其受保护资源的访问权限。 示例包括可访问 Azure AD 租户数据的 Azure AD Graph API，以及可访问邮件和日历等数据的 Office 365 API。 Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

与客户端应用程序一样，资源应用程序的标识配置是通过 Azure AD 租户中的[注册](#application-registration)来建立的，可提供应用程序和服务主体对象。 Microsoft 提供的某些 API（例如 Azure AD 图形 API）在预配期间将预先注册的服务主体设置为在所有租户中可用。

## <a name="roles"></a>角色

与[范围](#scopes)一样，角色提供某种方式让[资源服务器](#resource-server)控制其受保护资源的访问权限。 有两种类型的角色：“用户”角色为需要资源访问权限的用户/组实现基于角色的访问控制，“应用程序”角色为需要访问权限的[客户端应用程序](#client-application)实现相同的访问控制。

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. 也可通过 Azure 门户为用户分配“用户”角色，并配置用于访问“应用程序”角色的客户端[应用程序权限](#permissions)。

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>范围

与[角色](#roles)一样，范围提供某种方式让[资源服务器](#resource-server)控制其受保护资源的访问权限。 Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. 也可通过 Azure 门户配置用于访问范围的客户端应用程序[委托权限](#permissions)。

命名约定最佳实践是使用“resource.operation.constraint”格式。 For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>安全令牌

包含 OAuth2 令牌或 SAML 2.0 断言等声明的已签名文档。 For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>服务主体对象

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. 应用程序对象可全局（在关联的应用程序已获授予访问权限的所有租户中）*定义*应用程序的标识配置，并可作为模板来*派生*出其对应的服务主体对象，以便在运行时于本地（在特定租户）使用。

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>登录

[客户端应用程序](#client-application)启动最终用户身份验证并捕获相关状态，以便获取[安全令牌](#security-token)并将应用程序会话局限在该状态的过程。 状态可以包括多种项目，例如用户配置文件信息，以及派生自令牌声明的信息。

应用程序的登录功能通常用于实现单一登录 (SSO)。 该功能前面可能还会有一个“注册”功能，作为用户访问应用程序（在首次登录时）的入口点。 注册功能用于收集和保存用户特定的其他状态，并且可能需要[用户同意](#consent)。

## <a name="sign-out"></a>注销

使最终用户变成未身份验证状态的过程，解除用户在[登录](#sign-in)期间与[客户端应用程序](#client-application)会话关联的状态

## <a name="tenant"></a>tenant

Azure AD 目录的实例称为 Azure AD 租户。 它提供的一些功能包括：

* 适用于集成应用程序的注册表服务
* 对用户帐户和已注册应用程序进行身份验证
* 为各种协议（包括 OAuth2 和 SAML）提供支持所需的 REST 终结点，包括[授权终结点](#authorization-endpoint)、[令牌终结点](#token-endpoint)以及[多租户应用程序](#multi-tenant-application)使用的“通用”终结点。

在注册期间创建 Azure AD 租户/将 Azure AD 租户与 Azure 和 Office 365 订阅相关联，以便为该订阅提供标识和访问管理功能。 Azure 订阅管理员还可通过 Azure 门户创建其他 Azure AD 租户。 See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>令牌终结点

[授权服务器](#authorization-server)为了支持 OAuth2 [权限授予](#authorization-grant)而实现的终结点之一。 Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>基于用户代理的客户端

一种[客户端应用程序](#client-application)，例如单页应用程序 (SPA)，可从 Web 服务器下载代码并在用户代理（例如 Web 浏览器）中执行。 由于所有代码都在设备上执行，因此设备因为无法隐私/秘密地存储凭据而被视为“公共”客户端。 For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>用户主体

与服务主体对象用于表示应用程序实例的方式一样，用户主体对象是另一种类型的安全主体，它代表用户。 The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. 用户主体用于代表经身份验证的用户执行单一登录、记录[同意](#consent)委托、做出访问控制决策等操作。

## <a name="web-client"></a>Web 客户端

一类[客户端应用程序](#client-application)，可在 Web 服务器上执行所有代码，并可将凭据安全地存储在服务器上，充当“机密”客户端。 For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>后续步骤

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. 另外，还可在 [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=) 上找到关于如何快速启动和运行的代码示例及教程。

请使用以下评论部分提供反馈，帮助我们改进和编写此内容，包括有关新建定义或更新现有定义的请求！

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
