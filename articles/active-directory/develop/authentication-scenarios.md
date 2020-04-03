---
title: Microsoft 标识平台中的身份验证 | Azure
description: 了解 Microsoft 标识平台 (v2.0) 中的身份验证的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6191e67f097b5ab471c5b31eff11a0e570d1c990
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617027"
---
# <a name="authentication-basics"></a>身份验证基础知识

## <a name="what-is-authentication"></a>什么是身份验证

本文解释在创建受保护的 Web 应用、Web API 或创建调用受保护 Web API 的应用时需要了解的许多身份验证概念。 如果您看到一个不熟悉的术语，请尝试我们的[词汇表](developer-glossary.md)或我们的[Microsoft 身份平台视频](identity-videos.md)，这些视频涵盖了基本概念。

**身份验证**就是证明自己的身份的过程。 身份验证有时缩写为 AuthN。

**授权**是指授予经过身份验证的一方执行某项操作的权限的措施。 它指定了你可以访问的数据，以及可对该数据执行的操作。 授权有时缩写为 AuthZ。

无需创建每个需要维护自身用户名和密码信息的应用（需要在多个应用中添加或删除用户时，这会产生较高的管理负担），而可以让应用将这种责任委托给一个集中式标识提供者。

Azure Active Directory (Azure AD) 是云中的集中式标识提供者。 将身份验证和授权委派给它，可以执行条件访问策略（要求用户位于特定位置的条件访问策略、使用多重身份验证，以及允许用户登录一次，然后自动登录到共享同一集中目录的所有 Web 应用）。 此功能称为单一登录 (SSO)。

对于用户位于全球各地，且用户不一定从企业网络登录的应用而言，集中式标识提供者更为重要。 Azure AD 将对用户进行身份验证并提供访问令牌。 [访问令牌](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token)是由授权服务器颁发的安全令牌。 它包含有关用户和令牌用途的应用的信息;可用于访问 Web API 和其他受保护资源。

Microsoft 身份平台通过提供身份即服务来简化应用程序开发人员的身份验证，支持行业标准协议（如[OAuth 2.0](https://oauth.net/2/)和[OpenID Connect），](https://openid.net/connect/)以及针对不同平台的开源库，以帮助您快速开始编码。 它允许开发人员构建登录所有 Microsoft 标识的应用程序，获取令牌来调用[Microsoft Graph、](https://developer.microsoft.com/graph/)其他 Microsoft API 或开发人员已经构建的 API。 有关详细信息，请参阅 [Microsoft 标识平台的发展](about-microsoft-identity-platform.md)。

### <a name="tenants"></a>租户

云标识提供者为许多组织提供服务。 为使不同组织中的用户保持隔离，Azure AD 已分区成租户，每个组织有一个租户。

租户跟踪用户及其关联的应用。 Microsoft 标识平台还支持使用个人 Microsoft 帐户登录的用户。

Azure AD 还提供 Azure 活动目录 B2C，以便组织可以使用 Google 帐户等社交身份登录用户（通常是客户）。 有关详细信息，请参阅 [Azure Active Directory B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c)。

### <a name="security-tokens"></a>安全令牌

安全令牌包含有关用户和应用的信息。 Azure AD 使用包含声明的基于 JSON 的令牌 (JWT)。

声明向另一个实体（如[资源服务器](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)）提供有关一个实体（如[客户端应用程序](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)或[资源所有者](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)）的断言。

声明是中继有关令牌主题的事实的名称/值对。 例如，声明可能包含有关[由授权服务器](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server)进行身份验证的安全主体的事实。 给定令牌中存在的声明取决于许多内容，包括令牌类型、用于验证主题的凭据类型、应用程序配置等。

应用程序可以使用声明来完成各种任务，例如：

* 验证令牌
* 标识令牌主体的租户
* 显示用户信息
* 确定使用者的授权

声明由提供以下信息的键值对组成：

* 生成令牌的安全令牌服务器
* 生成令牌的日期
* 主题（如用户 -守护进程除外）
* 访问群体，这是为其生成令牌的应用
* 请求令牌的应用（客户端）。 在 Web 应用中，这可能与受众相同

有关详细信息的声明信息，请参阅[访问令牌](access-tokens.md)和 ID[令牌](id-tokens.md)。

由为其生成了令牌的应用、将登录用户的 Web 应用或所调用的 Web API 负责验证令牌。 令牌由安全令牌服务器 (STS) 使用私钥签名。 STS 发布相应的公钥。 若要验证令牌，应用需使用 STS 公钥验证签名，以验证签名是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，STS 会提供一对令牌：一个用于访问应用程序或受保护资源的访问令牌，以及一个在访问令牌即将过期时用于刷新访问令牌的刷新令牌。

访问令牌作为 `Authorization` 标头中的持有者令牌传递给 Web API。 应用可向 STS 提供刷新令牌，如果用户对应用的访问权限未吊销，则应用将取回新的访问令牌和新的刷新令牌。 用户离职的场景就是这样处理的。 当 STS 收到刷新令牌时，如果用户不再获得授权，则 STS 不会颁发另一个有效的访问令牌。

### <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的生成方式，客户端可以使用 Azure AD 支持的一种（或几种）身份验证流。 这些流可以生成各种令牌（id_tokens、刷新令牌、访问令牌）以及授权代码，并需要不同的令牌使其正常工作。 此图表提供概述：

|流向 | 需要 | id_token | 访问令牌 | 刷新令牌 | 授权代码 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL（其中 `response_mode` 是 `query` 或 `fragment`）传回浏览器而具有长度限制。  有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。  因此，这些令牌没有 `groups` 或 `wids` 声明。 

现在，您已经了解了基础知识，请继续阅读以了解标识应用模型和 API，了解预配在 Azure AD 中的工作原理，并获取有关 Azure AD 支持的常见方案的详细信息的链接。

## <a name="application-model"></a>应用程序模型

应用程序可以自行将用户登录，或者委托标识提供者登录。 有关 Azure AD 支持的登录方案，请参阅[身份验证流和应用方案](authentication-flows-app-scenarios.md)。

要使标识提供者知道某个用户有权访问特定的应用，必须同时将该用户和应用程序注册到标识提供者。 将应用程序注册到 Azure AD 时，需要提供应用程序的标识配置，使其能够与 Azure AD 集成。 注册应用程序还允许您：

* 在登录对话框中自定义应用程序的品牌。 这一点很重要，因为这是用户首次体验你的应用。
* 决定是否允许用户仅在用户属于您的组织时才登录。 这是一个单租户应用程序。 或者允许用户使用任何工作或学校帐户登录。 这是一个多租户应用程序。 您还可以允许个人 Microsoft 帐户，或来自LinkedIn、Google 等的社交帐户。
* 请求作用域权限。 例如，可以请求“user.read”范围，该授予读取已登录用户的个人资料的权限。
* 定义定义对 Web API 的访问的范围。 通常，当某个应用想要访问你的 API 时，它需要请求对你所定义的范围的权限。
* 与 Azure AD 共享机密，该机密向 Azure AD 证明应用的身份。  这适用于应用是机密客户端应用程序的情况。 机密客户端应用程序是可以安全保存凭据的应用程序。 它们需要使用受信任的后端服务器来存储凭据。

注册后，应用程序将获得一个唯一标识符，该标识符是应用在请求令牌时与 Azure AD 共享的。 如果应用程序是[机密客户端应用程序](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)，它也将共享机密或公钥*-取决于是否使用了证书或机密。

Microsoft 标识平台使用实现以下两项主要功能的模型来表示应用程序：

* 按其支持的身份验证协议标识应用
* 提供身份验证所需的所有标识符、URL、机密和相关信息

Microsoft 标识平台：

* 保存运行时支持身份验证所需的所有数据
* 保存所有数据，以决定应用可能需要访问哪些资源，以及应在什么情况下满足给定请求
* 提供用于在应用开发人员的租户中实现应用预配以及任何其他 Azure AD 租户的基础结构
* 在令牌请求期间处理用户同意并帮助跨租户动态预配应用

同意是资源所有者授权客户端应用程序代表资源所有者在特定权限下访问受保护资源的过程。 Microsoft 标识平台：

* 使用户和管理员能够动态地同意或拒绝应用以他们的名义访问资源。
* 使管理员能够最终决定允许执行哪些应用、哪些用户可以使用特定的应用，以及如何访问目录资源。

在 Microsoft 标识平台中，[应用程序对象](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object)描述应用程序。 在部署时，Microsoft 标识平台使用应用程序对象作为蓝图来创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)，它表示目录或租户中的应用程序的具体实例。 该服务主体定义应用在特定目标目录中可以实际执行的操作、使用者是谁、以及可以访问哪些资源等。 Microsoft 标识平台通过**许可**使用应用程序对象创建服务主体。

下图显示了征得同意后经过简化的 Microsoft 标识平台预配流程。 它显示两个租户：A 和 B. 租户 A 拥有应用程序。 租户 B 通过服务主体实例化该应用程序。  

![征得同意后经过简化的预配流程](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在此预配流程中：

1. 来自租户 B 的某个用户尝试使用该应用登录，授权终结点请求应用程序的令牌。
1. 获取并验证用于身份验证的用户凭据。
1. 系统提示用户许可该应用访问租户 B。
1. Microsoft 标识平台使用租户 A 中的应用程序对象作为在租户 B 中创建服务主体的蓝图。
1. 用户接收请求的令牌。

可对其他租户重复此过程。 租户 A 保留了应用（应用程序对象）的蓝图。 应用获得许可的所有其他租户中的用户和管理员通过每个租户中的相应服务主体对象保留对应用程序允许执行的操作的控制权。 有关详细信息，请参阅 [Microsoft 标识平台中的应用程序和服务主体对象](app-objects-and-service-principals.md)。

## <a name="web-app-sign-in-flow-with-azure-ad"></a>使用 Azure AD 的 Web 应用登录流

当用户在浏览器中导航到某个 Web 应用时，将发生以下情况：

* Web 应用确定用户是否已完成身份验证。
* 如果用户未完成身份验证，Web 应用将委托 Azure AD 将用户登录。 登录符合组织的策略，这可能意味着，系统会要求用户输入其凭据、使用多重身份验证，或者完全禁止使用密码（例如使用 Windows Hello）。
* 系统要求用户许可客户端应用所需的访问权限。 正因如此，需要在 Azure AD 中注册客户端应用，使 Azure AD 能够传送代表用户许可的访问权限的令牌。

用户成功完成身份验证后：

* Azure AD 将令牌发送到 Web 应用。
* 保存与 Azure AD 的域关联的 Cookie，其中包含浏览器 Cookie jar 中的用户标识。 下次应用使用浏览器导航到 Azure AD 授权终结点时，浏览器将提供该 Cookie，因此用户无需再次登录。 这也是实现 SSO 的方式。 Cookie 由 Azure AD 生成，只有 Azure AD 能够识别它。
* 然后，Web 应用验证令牌。 如果验证成功，Web 应用将显示受保护的页，并在浏览器的 Cookie jar 中保存会话 Cookie。 当用户导航到另一页时，Web 应用知道用户已基于会话 Cookie 进行身份验证。

以下序列图汇总了这种交互：

![Web 应用身份验证过程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 应用如何确定用户是否已完成身份验证

Web 应用开发人员可以指定是所有页还是只有特定的页需要身份验证。 例如，在 ASP.NET/ASP.NET Core 中，可以通过将 `[Authorize]` 属性添加到控制器操作来进行这种指定。 

此属性会导致 ASP.NET 检查是否存在包含用户标识的会话 Cookie。 如果 Cookie 不存在，ASP.NET 会将身份验证重定向到指定的标识提供者。 如果标识提供者是 Azure AD，则 Web 应用会将身份验证重定向到 `https://login.microsoftonline.com`，这会显示登录对话框。

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Web 应用如何将登录委托给 Azure AD 和获取令牌

用户身份验证通过浏览器发生。 OpenID 协议使用标准 HTTP 协议消息。
* Web 应用将 HTTP 302（重定向）发送到浏览器以使用 Azure AD。
* 对用户进行身份验证时，Azure AD 通过浏览器使用重定向将令牌发送到 Web 应用。
* 重定向由 Web 应用以重定向 URI 的形式提供。 此重定向 URI 将注册到 Azure AD 应用程序对象。 由于应用程序可部署到多个 URL，可能存在多个重定向 URI。 因此，Web 应用还需要指定要使用的重定向 URI。
* Azure AD 验证 Web 应用发送的重定向 URI 是否为应用的已注册重定向 URI 之一。

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>使用 Azure AD 的桌面和移动应用登录流

上面所述的流适用于桌面和移动应用程序，只是存在少许差异。

桌面和移动应用程序可以使用嵌入式 Web 控件或系统浏览器进行身份验证。 下图演示了桌面或移动应用如何使用 Microsoft 身份验证库 (MSAL) 获取访问令牌和调用 Web API。

![桌面应用的身份验证方式](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用浏览器获取令牌。 与 Web 应用一样，身份验证将委派给 Azure AD。

由于 Azure AD 与对 Web 应用一样在浏览器中保存相同的标识 Cookie，因此，如果本机应用或移动应用使用系统浏览器，则会立即在相应的 Web 应用上实现 SSO。

默认情况下，MSAL 使用系统浏览器。 例外情况是 .NET Framework 桌面应用程序，其中嵌入式控件用于提供更集成的用户体验。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Microsoft 标识平台开发人员术语表](developer-glossary.md)来熟悉常用术语。
* 请参阅[身份验证流和应用方案](authentication-flows-app-scenarios.md)来详细了解 Microsoft 标识平台支持的其他用户身份验证方案。
* 请参阅[MSAL 库](msal-overview.md)，了解 Microsoft 库，这些库可帮助您在单个简化的编程模型中开发与 Microsoft 帐户、Azure AD 帐户和 Azure AD B2C 用户一起工作的应用程序。
* 请参阅[将应用服务与 Microsoft 标识平台集成](/azure/app-service/configure-authentication-provider-aad)，了解如何为应用服务应用配置身份验证。
