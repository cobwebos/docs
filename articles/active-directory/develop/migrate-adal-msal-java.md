---
title: 适用于 Java 的 ADAL 到 MSAL 迁移指南 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何将 Azure Active Directory 身份验证库（ADAL） Java 应用迁移到 Microsoft 身份验证库（MSAL）。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e99cee3f21a4e0088fa97dcbec8fdcfdf982d80
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917380"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>适用于 Java 的 ADAL 到 MSAL 迁移指南

本文重点介绍将使用 Azure Active Directory 身份验证库（ADAL）的应用程序迁移到使用 Microsoft 身份验证库（MSAL）所需的更改。

适用于 Java 的 Microsoft 身份验证库（MSAL4J）和 Java Azure AD 身份验证库（ADAL4J）用于对 Azure AD 实体进行身份验证并从 Azure AD 请求令牌。 到现在为止，大多数开发人员都与 Azure AD 开发人员平台（1.0）合作，通过使用 Azure AD 身份验证库（ADAL）请求令牌来对 Azure AD 标识（工作和学校帐户）进行身份验证。

MSAL 具有以下优势：

- 由于它使用较新的 Microsoft 标识平台终结点，因此你可以通过 Azure AD 企业到消费者（B2C）来验证一组更广泛的 Microsoft 标识，如 Azure AD 标识、Microsoft 帐户以及社交和本地帐户。
- 你的用户将获得最佳的单一登录体验。
- 你的应用程序可以启用增量许可，并支持条件性访问。

MSAL for Java 是我们建议用于 Microsoft 标识平台的身份验证库。 不会在 ADAL4J 上实现新功能。 今后的所有工作都侧重于改进 MSAL。

## <a name="differences"></a>差异

如果你使用的是面向开发人员（1.0）终结点（ADAL4J）的 Azure AD，则可能需要阅读[有关 Microsoft 标识平台（v2.0）终结点的不同之处](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

## <a name="scopes-not-resources"></a>范围不是资源

ADAL4J 获取资源的令牌，而 MSAL for Java 将获取作用域的令牌。 Java 类的多个 MSAL 需要作用域参数。 此参数是一个字符串列表，这些字符串声明所请求的权限和资源。 请参阅[Microsoft Graph 的作用域](https://docs.microsoft.com/graph/permissions-reference)以查看示例范围。

## <a name="core-classes"></a>核心类

在 ADAL4J 中，`AuthenticationContext` 类表示与安全令牌服务（STS）或授权服务器（通过颁发机构）的连接。 但是，MSAL for Java 是围绕客户端应用程序设计的。 它提供两个单独的类： `PublicClientApplication` 和 `ConfidentialClientApplication`，以表示客户端应用程序。  后者（`ConfidentialClientApplication`）表示一个应用程序，该应用程序旨在安全地维护机密，如后台应用程序的应用程序标识符。

下表显示了 ADAL4J 函数如何映射到新的 MSAL for Java 函数：

| ADAL4J 方法| MSAL4J 方法|
|------|-------|
|acquireToken （字符串资源，ClientCredential credential，Authenticationcallback 传递给回调） | acquireToken （ClientCredentialParameters）|
|acquireToken （字符串资源，ClientAssertion 断言，Authenticationcallback 传递给回调）|acquireToken （ClientCredentialParameters）|
|acquireToken （字符串资源，AsymmetricKeyCredential credential，Authenticationcallback 传递给回调）|acquireToken （ClientCredentialParameters）|
|acquireToken （字符串资源，字符串 clientId，字符串用户名，字符串密码，Authenticationcallback 传递给回调）| acquireToken （UsernamePasswordParameters）|
|acquireToken （字符串资源，字符串 clientId，字符串用户名，字符串密码 = null，Authenticationcallback 传递给回调）|acquireToken （IntegratedWindowsAuthenticationParameters）|
|acquireToken （字符串资源，UserAssertion userAssertion，ClientCredential credential，Authenticationcallback 传递给回调）| acquireToken （OnBehalfOfParameters）|
|acquireTokenByAuthorizationCode （） | acquireToken （AuthorizationCodeParameters） |
| acquireDeviceCode （）和 acquireTokenByDeviceCode （）| acquireToken （DeviceCodeParameters）|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount 而不是 IUser

ADAL4J 操作用户。 尽管用户代表单个人员或软件代理，但它可以在 Microsoft 标识系统中有一个或多个帐户。 例如，用户可能有多个 Azure AD、Azure AD B2C 或 Microsoft 个人帐户。

MSAL for Java 通过 `IAccount` 接口定义帐户的概念。 这是 ADAL4J 的一项重大更改，但这是一项很好的更改，因为它可以捕获同一个用户可以拥有多个帐户的事实，甚至在不同 Azure AD 目录中。 MSAL for Java 在来宾方案中提供了更好的信息，因为提供了 home 帐户信息。

## <a name="cache-persistence"></a>缓存持久性

ADAL4J 不支持令牌缓存。
MSAL for Java 添加了一个[令牌缓存](msal-acquire-cache-tokens.md)，可通过自动刷新过期令牌来简化令牌生存期的管理，并防止用户在可能的情况中提供凭据。

## <a name="common-authority"></a>共同颁发机构

在1.0 版中，如果使用的是 `https://login.microsoftonline.com/common` 机构，则用户可以使用任何 Azure Active Directory （AAD）帐户登录（适用于任何组织）。

如果你使用 v2.0 中的 `https://login.microsoftonline.com/common` 颁发机构，则用户可以使用任何 AAD 组织，甚至 Microsoft 个人帐户（MSA）登录。 在适用于 Java 的 MSAL 中，如果要将登录限制为任何 AAD 帐户，则需使用 `https://login.microsoftonline.com/organizations` 颁发机构（与 ADAL4J 相同的行为）。 若要指定颁发机构，请在创建 `PublicClientApplication` 类时设置[PublicClientApplication](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html)方法中的 `authority` 参数。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 令牌

v1.0 终结点（由 ADAL 使用）只发出 v1.0 令牌。

V2.0 终结点（由 MSAL 使用）可以发出 v1.0 和 v2.0 令牌。 开发人员可以使用 Web API 应用程序清单的属性来选择接受的令牌版本。 请参阅[应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)参考文档中的 `accessTokenAcceptedVersion`。

有关1.0 和 v2.0 令牌的详细信息，请参阅[Azure Active Directory 访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

## <a name="adal-to-msal-migration"></a>ADAL 到 MSAL 的迁移

在 ADAL4J 中，用户可以使用刷新令牌来缓存这些令牌。 然后，他们将使用 `AcquireTokenByRefreshToken()` 来启用解决方案，如实现长时间运行的服务，这些服务将在用户不再连接时代表用户刷新仪表板。

MSAL for Java 出于安全原因不会公开刷新令牌。 相反，MSAL 会为你处理刷新令牌的情况。

MSAL for Java 提供了一个 API，可让你将使用 ADAL4j 获取的刷新令牌迁移到 ClientApplication： [acquireToken （RefreshTokenParameters）](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)中。 使用此方法可以结合所需的任何范围（资源）提供以前用过的刷新令牌。 将为新的刷新令牌进行交换，并缓存该令牌以供你的应用程序使用。

以下代码段显示了机密客户端应用程序中的一些迁移代码：

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

当新的刷新令牌存储在缓存中时，`IAuthenticationResult` 将返回访问令牌和 ID 令牌。 应用程序现在还将包含一个 IAccount：

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

若要使用目前在缓存中的令牌，请调用：

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
