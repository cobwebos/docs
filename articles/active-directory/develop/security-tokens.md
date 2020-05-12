---
title: 安全令牌 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台（v2.0）中安全令牌的基本知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: def198a15710d0aff4a943300eedc338a7772e46
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115789"
---
# <a name="security-tokens"></a>安全令牌

集中标识提供者特别适用于在全球各地有用户登录不一定从企业网络登录的应用。 Microsoft 标识平台会对用户进行身份验证，并提供允许[客户端应用程序](developer-glossary.md#client-application)访问[资源服务器](developer-glossary.md#resource-server)上受保护的资源的安全令牌，如[访问令牌](developer-glossary.md#access-token)、[刷新令牌](developer-glossary.md#refresh-token)和[ID 令牌](developer-glossary.md#id-token)。

**访问令牌**是一个安全令牌，由[授权服务器](developer-glossary.md#authorization-server)作为[OAuth 2.0](active-directory-v2-protocols.md)流的一部分进行颁发。 其中包含有关该令牌所针对的用户和应用的信息；可使用它访问 Web API 和其他受保护资源。 若要详细了解 Microsoft 标识平台如何颁发访问令牌，请参阅[访问令牌](access-tokens.md)。

访问令牌仅在短时间内有效，因此，在颁发访问令牌的同时，授权服务器有时会颁发**刷新令牌**。 然后，客户端应用程序可以在需要时为新的访问令牌交换此刷新令牌。 若要详细了解 Microsoft 标识平台如何使用刷新令牌来废除权限，请参阅[令牌吊销](access-tokens.md#token-revocation)。

**ID 令牌**作为[OpenID connect](v2-protocols-oidc.md)流的一部分发送到客户端应用程序。 它们可以一起发送来代替访问令牌，可供客户端用来对用户进行身份验证。 若要了解有关 Microsoft 标识平台如何颁发 ID 令牌的详细信息，请参阅[id 令牌](id-tokens.md)。

> [!NOTE]
> 本文讨论 OAuth2 和 OpenID Connect 协议使用的安全令牌。 许多企业应用程序使用 SAML 对用户进行身份验证。 有关 SAML 断言的信息，请参阅[AZURE AD saml 令牌参考](reference-saml-tokens.md)。

## <a name="validating-security-tokens"></a>验证安全令牌

由为其生成了令牌的应用、将登录用户的 Web 应用或所调用的 Web API 负责验证令牌。 令牌由**安全令牌服务器（STS）** 使用私钥进行签名。 STS 发布相应的公钥。 若要验证令牌，应用需使用 STS 公钥验证签名，以验证签名是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，STS 提供一对令牌：

* 访问应用程序或受保护资源的访问令牌，
* 用于在访问令牌接近过期时刷新访问令牌的刷新令牌。

访问令牌作为 `Authorization` 标头中的持有者令牌传递给 Web API。 应用可向 STS 提供刷新令牌，如果用户对应用的访问权限未吊销，则应用将取回新的访问令牌和新的刷新令牌。 用户离职的场景就是这样处理的。 当 STS 收到刷新令牌时，如果用户不再获得授权，则 STS 不会颁发另一个有效的访问令牌。

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web 令牌（Jwt）和声明

Microsoft 标识平台将安全令牌实现为包含**声明**的**JSON Web 令牌（jwt）** 。 由于使用 Jwt 作为安全令牌，因此这种形式的身份验证有时称为**JWT 身份验证**。

[声明](developer-glossary.md#claim)向另一个实体（例如资源服务器）提供有关一个实体（例如客户端应用程序或[资源所有者](developer-glossary.md#resource-owner)）的断言。 声明还可以称为 JWT 声明或 JSON Web 令牌声明。

声明是对令牌主体相关事实进行中继的名称/值对。 例如，声明可能包含由授权服务器进行身份验证的安全主体的相关事实。 给定令牌中存在的声明取决于许多因素，包括令牌类型、用于验证主体身份的凭据类型和应用程序配置等。

应用程序可以使用声明来完成各种任务，例如：

* 验证令牌
* 标识令牌使用者的[租户](developer-glossary.md#tenant)
* 显示用户信息
* 确定使用者的授权

声明由提供如下信息的键值对组成：

* 生成了令牌的安全令牌服务器
* 令牌生成日期
* 使用者，例如用户（守护程序除外）
* 受众，即，为其生成了令牌的应用
* 请求了令牌的应用（客户端）。 对于 Web 应用，这可能与受众相同

若要详细了解 Microsoft 标识平台如何实现令牌和声明信息，请参阅[访问令牌](access-tokens.md)和[ID 令牌](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的构建方式，它可以使用 Microsoft 标识平台支持的一个（或多个）身份验证流。 这些流可以生成各种令牌（ID 令牌、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：

|流向 | 需要 | ID 令牌 | 访问令牌 | 刷新令牌 | 授权代码 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="next-steps"></a>后续步骤

有关涉及身份验证和授权的其他主题，请执行以下操作：

* 若要了解 Microsoft 标识平台中身份验证和授权的基本概念，请参阅[身份验证与授权](authentication-vs-authorization.md)。
* 请参阅[应用程序模型](application-model.md)，了解注册应用程序的过程，使其能够与 Microsoft 标识平台集成。
* 请参阅[应用登录流](app-sign-in-flow.md)，了解 Microsoft 标识平台中的 web、桌面和移动应用的登录流。
