---
title: Microsoft 标识平台中的身份验证 | Azure
description: 了解 Microsoft 标识平台（v2.0）中身份验证的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161735"
---
# <a name="authentication-basics"></a>身份验证基础知识

本文介绍了在创建受保护的 web 应用、web Api 或调用受保护的 web Api 的应用时需要了解的许多身份验证概念。 如果你看到不熟悉的术语，请尝试我们的[术语表](developer-glossary.md)或[Microsoft 标识平台视频](identity-videos.md)，其中介绍了基本概念。

## <a name="authentication-vs-authorization"></a>身份验证与授权

**身份验证**就是证明自己的身份的过程。 身份验证有时缩写为 AuthN。 Microsoft 标识平台实现了用于处理身份验证的[OpenID connect](https://openid.net/connect/)协议。

**授权**是指授予经过身份验证的一方执行某项操作的权限的措施。 它指定了你可以访问的数据，以及可对该数据执行的操作。 授权有时缩写为 AuthZ。 Microsoft 标识平台实现了[OAuth 2.0](https://oauth.net/2/)协议来处理授权。

无需创建每个需要维护自身用户名和密码信息的应用（需要在多个应用中添加或删除用户时，这会产生较高的管理负担），而可以让应用将这种责任委托给一个集中式标识提供者。

Azure Active Directory (Azure AD) 是云中的集中式标识提供者。 将身份验证和授权委派给它可以实现这样的方案：要求用户位于特定位置、使用多重身份验证的条件性访问策略，以及使用户能够登录一次，然后自动登录到共享同一集中目录的所有 web 应用。 此功能称为**单一登录（SSO）**。

Microsoft 标识平台通过提供标识即服务简化了应用程序开发人员的身份验证和授权，并支持行业标准协议（例如 OAuth 2.0 和 OpenID Connect）以及用于不同平台的开源库来帮助你快速开始编码。 它允许开发人员生成应用程序，这些应用程序可用于注册所有 Microsoft 标识，获取用于调用[Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft api 或开发人员构建的 api 的令牌。 有关详细信息，请参阅 [Microsoft 标识平台的发展](about-microsoft-identity-platform.md)。

## <a name="security-tokens"></a>安全令牌

集中标识提供者对于在全球各地不需要从企业网络登录的用户来说尤为重要。 Microsoft 标识平台会对用户进行身份验证，并提供允许[客户端应用程序](developer-glossary.md#client-application)访问[资源服务器](developer-glossary.md#resource-server)上受保护的资源的安全令牌，如[访问令牌](developer-glossary.md#access-token)、[刷新令牌](developer-glossary.md#refresh-token)和[ID 令牌](developer-glossary.md#id-token)。

**访问令牌**是由授权服务器颁发的安全令牌。 它包含有关标记所针对的用户和应用的信息;可用于访问 web Api 和其他受保护的资源。 若要详细了解 Microsoft 标识平台如何颁发访问令牌，请参阅[访问令牌](access-tokens.md)。

访问令牌仅在短时间内有效，因此，在颁发访问令牌的同时，授权服务器有时会颁发**刷新令牌**。 然后，客户端应用程序可以在需要时为新的访问令牌交换此刷新令牌。 若要详细了解 Microsoft 标识平台如何使用刷新令牌来废除权限，请参阅[令牌吊销](access-tokens.md#token-revocation)。

**ID 令牌**作为[OpenID connect](v2-protocols-oidc.md)流的一部分发送到客户端应用程序。 它们可以一起发送来代替访问令牌，可供客户端用来对用户进行身份验证。 若要了解有关 Microsoft 标识平台如何颁发 ID 令牌的详细信息，请参阅[id 令牌](id-tokens.md)。

### <a name="validating-security-tokens"></a>验证安全令牌

这取决于生成令牌的应用、登录用户的 web 应用或正在调用的 web API，用于验证令牌。 令牌由安全令牌服务器 (STS) 使用私钥签名。 STS 发布相应的公钥。 若要验证令牌，应用需使用 STS 公钥验证签名，以验证签名是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，STS 提供一对令牌：

* 访问应用程序或受保护资源的访问令牌，
* 用于在访问令牌接近过期时刷新访问令牌的刷新令牌。

访问令牌作为`Authorization`标头中的持有者令牌传递到 web API。 应用可向 STS 提供刷新令牌，如果用户对应用的访问权限未吊销，则应用将取回新的访问令牌和新的刷新令牌。 用户离职的场景就是这样处理的。 当 STS 收到刷新令牌时，如果用户不再获得授权，则 STS 不会颁发另一个有效的访问令牌。

### <a name="json-web-tokens-jwts-and-claims"></a>JSON Web 令牌（Jwt）和声明

Microsoft 标识平台将安全令牌实现为包含声明的 JSON Web 令牌（Jwt）。

[声明](developer-glossary.md#claim)向另一个实体（例如资源服务器）提供有关一个实体（例如客户端应用程序或[资源所有者](developer-glossary.md#resource-owner)）的断言。

声明是中继令牌主体相关事实的名称/值对。 例如，声明可能包含有关[授权服务器](developer-glossary.md#authorization-server)进行身份验证的安全主体的事实。 给定令牌中存在的声明取决于许多因素，包括令牌类型、用于验证主体身份的凭据类型和应用程序配置等。

应用程序可以使用声明来完成各种任务，例如：

* 验证令牌
* 标识令牌使用者的租户
* 显示用户信息
* 确定使用者的授权

声明由键/值对组成，这些键值对提供如下信息：

* 生成令牌的安全令牌服务器
* 生成令牌的日期
* Subject （如用户除外）
* 受众，它是为其生成令牌的应用程序
* 请求令牌的应用（客户端）。 对于 web 应用，这可能与受众相同

若要详细了解 Microsoft 标识平台如何实现令牌和声明信息，请参阅[访问令牌](access-tokens.md)和[ID 令牌](id-tokens.md)。

### <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的构建方式，它可以使用 Microsoft 标识平台支持的一个（或多个）身份验证流。 这些流可以生成各种令牌（id_tokens、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：

|流向 | 需要 | id_token | 访问令牌 | 刷新令牌 | 授权代码 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="tenants"></a>租户

云标识提供者为许多组织提供服务。 为使不同组织中的用户保持隔离，Azure AD 已分区成租户，每个组织有一个租户。

租户跟踪用户及其关联的应用。 Microsoft 标识平台还支持用个人 Microsoft 帐户登录的用户。

Azure AD 还提供 Azure Active Directory B2C 以便组织可以使用诸如 Google 帐户之类的社交标识登录用户（通常为客户）。 有关详细信息，请参阅 [Azure Active Directory B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c)。

现在，你已了解基础知识，请阅读以了解标识应用模型和 API，了解如何在 Microsoft 标识平台中进行预配，并获取有关 Microsoft 标识平台支持的常见方案的详细信息的链接。

## <a name="application-model"></a>应用程序模型

应用程序可以自行将用户登录，或者委托标识提供者登录。 若要了解 Microsoft 标识平台支持的登录方案，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。

要使标识提供者知道某个用户有权访问特定的应用，必须同时将该用户和应用程序注册到标识提供者。 向 Azure AD 注册应用程序时，将为应用程序提供标识配置，使其能够与 Microsoft 标识平台集成。 注册应用程序还允许：

* 在登录对话框中自定义应用程序的商标。 这一点很重要，因为这是用户首次体验你的应用。
* 决定是否只允许用户登录到你的组织。 这是一个单租户应用程序。 或者允许用户使用任何工作或学校帐户登录。 这是一个多租户应用程序。 你还可以允许个人 Microsoft 帐户，或来自 LinkedIn、Google 的社交帐户，等等。
* 请求范围权限。 例如，可以请求“user.read”范围，该授予读取已登录用户的个人资料的权限。
* 定义定义对 web API 的访问权限的作用域。 通常，当某个应用想要访问你的 API 时，它需要请求对你所定义的范围的权限。
* 与 Microsoft 标识平台共享机密，以证实应用的标识。  这适用于应用是机密客户端应用程序的情况。 机密客户端应用程序是可以安全保存凭据的应用程序。 它们需要使用受信任的后端服务器来存储凭据。

注册后，将为应用程序提供一个唯一的标识符，该标识符在请求令牌时与 Microsoft 标识平台共享。 如果应用是[机密客户端应用程序](developer-glossary.md#client-application)，它还会共享机密或公钥 *-具体取决于是否使用了证书或机密。

Microsoft 标识平台使用满足以下两个主要功能的模型来表示应用程序：

* 根据应用程序所支持的身份验证协议来识别应用
* 提供进行身份验证所需的所有标识符、Url、机密和相关信息

Microsoft 标识平台：

* 保存支持在运行时进行身份验证所需的所有数据
* 保存所有数据以确定应用可能需要访问的资源，以及在什么情况下应满足给定请求
* 提供基础结构，用于实现应用程序开发人员租户中的应用预配和任何其他 Azure AD 租户
* 在令牌请求期间处理用户同意并帮助跨租户动态预配应用

同意是资源所有者授权客户端应用程序代表资源所有者在特定权限下访问受保护资源的过程。 Microsoft 标识平台：

* 使用户和管理员能够动态地同意或拒绝应用以他们的名义访问资源。
* 使管理员能够最终决定允许执行哪些应用、哪些用户可以使用特定的应用，以及如何访问目录资源。

在 Microsoft 标识平台中，[应用程序对象](developer-glossary.md#application-object)描述应用程序。 在部署时，Microsoft 标识平台使用应用程序对象作为蓝图来创建[服务主体，该服务主体](developer-glossary.md#service-principal-object)表示目录或租户中的应用程序的具体实例。 该服务主体定义应用在特定目标目录中可以实际执行的操作、使用者是谁、以及可以访问哪些资源等。 Microsoft 标识平台通过**许可**使用应用程序对象创建服务主体。

下图显示了征得同意后经过简化的 Microsoft 标识平台预配流程。 其中显示了两个租户： *A*和*B*。

* *租户 A*拥有该应用程序。
* *租户 B*通过服务主体实例化应用程序。

![征得同意后经过简化的预配流程](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在此预配流程中：

1. 来自租户 B 的某个用户尝试使用该应用登录，授权终结点请求应用程序的令牌。
1. 获取并验证用于身份验证的用户凭据。
1. 系统提示用户许可该应用访问租户 B。
1. Microsoft 标识平台使用租户 A 中的应用程序对象作为在租户 B 中创建服务主体的蓝图。
1. 用户接收请求的令牌。

可对其他租户重复此过程。 租户 A 保留了应用（应用程序对象）的蓝图。 应用获得许可的所有其他租户中的用户和管理员通过每个租户中的相应服务主体对象保留对应用程序允许执行的操作的控制权。 有关详细信息，请参阅 [Microsoft 标识平台中的应用程序和服务主体对象](app-objects-and-service-principals.md)。

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Microsoft 标识平台的 Web 应用登录流

当用户在浏览器中导航到某个 Web 应用时，将发生以下情况：

* Web 应用确定用户是否已完成身份验证。
* 如果用户未完成身份验证，Web 应用将委托 Azure AD 将用户登录。 登录符合组织的策略，这可能意味着，系统会要求用户输入其凭据、使用多重身份验证，或者完全禁止使用密码（例如使用 Windows Hello）。
* 系统要求用户许可客户端应用所需的访问权限。 这就是需要在 Azure AD 中注册客户端应用程序的原因，以便 Microsoft 标识平台可以提供令牌，这些令牌代表用户同意的访问权限。

用户成功完成身份验证后：

* Microsoft 标识平台会将令牌发送到 web 应用。
* 保存与 Azure AD 的域关联的 Cookie，其中包含浏览器 Cookie jar 中的用户标识。 下一次应用使用浏览器导航到 Microsoft 标识平台授权终结点时，浏览器会显示该 cookie，这样用户就无需再次登录。 这也是实现 SSO 的方式。 Cookie 由 Azure AD 生成，只有 Azure AD 能够识别它。
* 然后，Web 应用验证令牌。 如果验证成功，Web 应用将显示受保护的页，并在浏览器的 Cookie jar 中保存会话 Cookie。 当用户导航到另一页时，Web 应用知道用户已基于会话 Cookie 进行身份验证。

以下序列图汇总了这种交互：

![Web 应用身份验证过程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 应用如何确定用户是否已完成身份验证

Web 应用开发人员可以指定是所有页还是只有特定的页需要身份验证。 例如，在 ASP.NET/ASP.NET Core 中，可以通过将 `[Authorize]` 属性添加到控制器操作来进行这种指定。

此属性会导致 ASP.NET 检查是否存在包含用户标识的会话 Cookie。 如果 Cookie 不存在，ASP.NET 会将身份验证重定向到指定的标识提供者。 如果标识提供者是 Azure AD，则 Web 应用会将身份验证重定向到 `https://login.microsoftonline.com`，这会显示登录对话框。

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Web 应用如何委托登录 Microsoft 标识平台和获取令牌

用户身份验证通过浏览器发生。 OpenID 协议使用标准 HTTP 协议消息。

* Web 应用将 HTTP 302 （重定向）发送到浏览器，以使用 Microsoft 标识平台。
* 对用户进行身份验证时，Microsoft 标识平台使用重定向通过浏览器将令牌发送到 web 应用。
* 重定向由 Web 应用以重定向 URI 的形式提供。 此重定向 URI 将注册到 Azure AD 应用程序对象。 由于应用程序可部署到多个 URL，可能存在多个重定向 URI。 因此，web 应用还需要指定要使用的重定向 URI。
* Azure AD 验证 Web 应用发送的重定向 URI 是否为应用的已注册重定向 URI 之一。

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>桌面和移动应用程序与 Microsoft 标识平台的登录流

上面所述的流适用于桌面和移动应用程序，只是存在少许差异。

桌面和移动应用程序可以使用嵌入式 Web 控件或系统浏览器进行身份验证。 下图演示了桌面或移动应用如何使用 Microsoft 身份验证库 (MSAL) 获取访问令牌和调用 Web API。

![桌面应用的身份验证方式](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用浏览器获取令牌。 对于 web 应用，身份验证将委托给 Microsoft 标识平台。

由于 Azure AD 与对 Web 应用一样在浏览器中保存相同的标识 Cookie，因此，如果本机应用或移动应用使用系统浏览器，则会立即在相应的 Web 应用上实现 SSO。

默认情况下，MSAL 使用系统浏览器。 异常是 .NET Framework 桌面应用程序，其中嵌入的控件用于提供更集成的用户体验。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Microsoft 标识平台开发人员术语表](developer-glossary.md)来熟悉常用术语。
* 若要详细了解 Microsoft 标识平台支持的用户，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。
* 请参阅[MSAL 库](msal-overview.md)，了解 microsoft 库，这些库可帮助你开发使用 microsoft 帐户、Azure AD 帐户和 Azure AD B2C 用户在单个简化编程模型中的应用程序。
* 若要了解如何为应用服务应用配置身份验证，请参阅将[应用服务与 Microsoft 标识平台集成](/azure/app-service/configure-authentication-provider-aad)。
