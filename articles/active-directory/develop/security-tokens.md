---
title: 安全令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台 (v2.0) 中的安全令牌的基础知识。
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
ms.openlocfilehash: 88dc4bb86459cd0390c4c01deb871aa93e39c6d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84266740"
---
# <a name="security-tokens"></a>安全令牌

对于用户位于全球各地，且用户不一定从企业网络登录的应用而言，集中式标识提供者尤其有用。 Microsoft 标识平台对用户进行身份验证并提供安全令牌，例如[访问令牌](developer-glossary.md#access-token)、[刷新令牌](developer-glossary.md#refresh-token)和 [ID 令牌](developer-glossary.md#id-token)，这些令牌允许[客户端应用程序](developer-glossary.md#client-application)访问[资源服务器](developer-glossary.md#resource-server)上受保护的资源。

“访问令牌”是由[授权服务器](developer-glossary.md#authorization-server)作为 [OAuth 2.0](active-directory-v2-protocols.md) 流的一部分颁发的安全令牌。 其中包含有关该令牌所针对的用户和应用的信息；可使用它访问 Web API 和其他受保护资源。 若要详细了解 Microsoft 标识平台如何颁发访问令牌，请参阅[访问令牌](access-tokens.md)。

访问令牌只在短时间内有效，因此授权服务器有时会在颁发访问令牌的同时颁发“刷新令牌”。 然后，客户端应用程序可以在需要时使用此刷新令牌交换新的访问令牌。 若要详细了解 Microsoft 标识平台如何使用刷新令牌来撤销权限，请参阅[令牌吊销](access-tokens.md#token-revocation)。

“ID 令牌”作为 [OpenID Connect](v2-protocols-oidc.md) 流的一部分中发送到客户端应用程序。 它们可以一起发送来代替访问令牌，可供客户端用来对用户进行身份验证。 若要详细了解 Microsoft 标识平台如何颁发 ID 令牌，请参阅 [ID 令牌](id-tokens.md)。

> [!NOTE]
> 本文讨论 OAuth2 和 OpenID Connect 协议使用的安全令牌。 许多企业应用程序使用 SAML 对用户进行身份验证。 有关 SAML 断言的信息，请参阅 [AZURE AD saml 令牌参考](reference-saml-tokens.md) 。

## <a name="validating-security-tokens"></a>验证安全令牌

由为其生成了令牌的应用、将登录用户的 Web 应用或所调用的 Web API 负责验证令牌。 令牌由“安全令牌服务器 (STS)”使用私钥签名。 STS 发布相应的公钥。 若要验证令牌，应用需使用 STS 公钥验证签名，以验证签名是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，STS 提供一对令牌：

* 访问应用程序或受保护资源的访问令牌，以及
* 用于在访问令牌即将过期时刷新访问令牌的刷新令牌。

访问令牌作为 `Authorization` 标头中的持有者令牌传递给 Web API。 应用可向 STS 提供刷新令牌，如果用户对应用的访问权限未吊销，则应用将取回新的访问令牌和新的刷新令牌。 用户离职的场景就是这样处理的。 当 STS 收到刷新令牌时，如果用户不再获得授权，则 STS 不会颁发另一个有效的访问令牌。

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web 令牌 (JWT) 和声明

Microsoft 标识平台将安全令牌实现为包含“声明”的“JSON Web 令牌 (JWT)” 。 由于 JWT 用作安全令牌，这种形式的身份验证有时称为“JWT 身份验证”。

[声明](developer-glossary.md#claim)将有关某个实体（例如客户端应用程序或[资源所有者](developer-glossary.md#resource-owner)）的断言提供给另一个实体（例如资源服务器）。 声明也可以称为 JWT 声明或 JSON Web 令牌声明。

声明是对令牌主体相关事实进行中继的名称/值对。 例如，声明可能包含由授权服务器进行身份验证的安全主体的相关事实。 给定令牌中提供的声明取决于许多事项，包括令牌类型、用于对使用者进行身份验证的凭据类型，以及应用程序配置等等。

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

若要详细了解 Microsoft 标识平台如何实现令牌和声明信息，请参阅[访问令牌](access-tokens.md)和 [ID 令牌](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的生成方式，客户端可以使用 Microsoft 标识平台支持的一种（或几种）身份验证流。 这些流可以生成各种令牌（ID 令牌、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：

|流向 | 需要 | ID 令牌 | 访问令牌 | 刷新令牌 | 授权代码 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="next-steps"></a>后续步骤

有关其他涉及身份验证和授权基础知识的主题：

* 参阅[身份验证与授权](authentication-vs-authorization.md)，了解 Microsoft 标识平台中身份验证和授权的基本概念。
* 参阅[应用程序模型](application-model.md)，了解注册应用程序以便它可以与 Microsoft 标识平台集成的过程。
* 参阅[应用登录流](app-sign-in-flow.md)，了解 Microsoft 标识平台中 Web、桌面和移动应用的登录流。
