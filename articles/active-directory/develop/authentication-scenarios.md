---
title: Microsoft 标识平台中的身份验证 | Azure
description: 了解 Microsoft 标识平台（v2.0）中身份验证的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff9af6a6f51ca89fa7e852609ceed1ee02c6e23f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294348"
---
# <a name="authentication-basics"></a>身份验证基础知识

## <a name="what-is-authentication"></a>什么是身份验证

本文介绍了在创建受保护的 web 应用、web Api 或调用受保护的 Web Api 的应用时需要了解的许多身份验证概念。

**身份验证**是证明您的身份的过程。 身份验证有时缩写为 AuthN。

**授权**是指向经过身份验证的参与方授予执行某些操作的权限。 它指定了允许访问的数据，以及可以对该数据执行的操作。 授权有时缩写为 AuthZ。

应用程序不是创建每个应用程序来维护自己的用户名和密码信息，而是需要在多个应用程序中添加或删除用户时，这会产生较高的管理负担，应用可以将该责任委派给一个集中的标识提供者。

Azure Active Directory （Azure AD）是云中的集中式标识提供者。 将身份验证和授权委派给它可以实现这样的方案：要求用户位于特定位置、使用多重身份验证的条件性访问策略，以及允许用户登录一次，然后自动登录到共享同一集中式目录的所有 web 应用。 此功能称为单一登录（SSO）。

集中标识提供者更重要的是，其用户位于全球各地的用户不需要从企业网络登录。 Azure AD 对用户进行身份验证并提供访问令牌。 访问令牌是由授权服务器颁发的安全令牌。 它包含有关标记所针对的用户和应用的信息，可用于访问 Web Api 和其他受保护的资源。

Microsoft 标识平台通过提供标识即服务简化了应用程序开发人员的身份验证，并支持行业标准协议（例如 OAuth 2.0 和 OpenID Connect），并提供了不同的开源库有助于快速开始编码的平台。 开发人员可以通过它来生成应用程序，以便进行所有 Microsoft 标识的登录，以及获取令牌来调用 Microsoft Graph、其他 Microsoft API 或者开发人员生成的 API。 有关详细信息，请参阅[Microsoft 标识平台演变](about-microsoft-identity-platform.md)。

### <a name="tenants"></a>租户

云标识提供者可为许多组织提供服务。 为了使不同组织中的用户保持独立，Azure AD 分为租户，每个组织一个租户。

租户跟踪用户及其关联的应用。 Microsoft 标识平台还支持用个人 Microsoft 帐户登录的用户。

Azure AD 还提供 Azure Active Directory B2C 以便组织可以使用诸如 Google 帐户之类的社交标识登录用户（通常为客户）。 有关详细信息，请参阅[Azure Active Directory B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c)。

### <a name="security-tokens"></a>安全令牌

安全令牌包含用户和应用的相关信息。 Azure AD 使用包含声明的基于 JSON 的标记（Jwt）。 声明向另一个实体提供有关实体的断言。 应用程序可以对各种任务使用声明，如：

* 验证令牌
* 标识使用者的目录租户
* 显示用户信息
* 确定使用者的授权

声明由键/值对组成，这些键值对提供如下信息：

- 生成令牌的安全令牌服务器。
- 生成令牌时的日期。
- 主题，例如用户（守护程序除外）。
- 受众，即为其生成令牌的应用。
- 请求令牌的应用程序（客户端）。 对于 web 应用，这可能与受众相同。

有关更详细的声明信息，请参阅[访问令牌](access-tokens.md)和[ID 令牌](id-tokens.md)。

这取决于生成令牌的应用、登录用户的 web 应用或正在调用的 Web API，用于验证令牌。 令牌由安全令牌服务器（STS）使用私钥进行签名。 STS 发布相应的公钥。 若要验证令牌，应用程序将使用 STS 公钥验证签名，验证签名是否是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，STS 提供一对令牌：用于访问应用程序或受保护资源的访问令牌，以及在访问令牌接近过期时用于刷新访问令牌的刷新令牌。 

访问令牌作为 `Authorization` 标头中的持有者令牌传递到 Web API。 应用可向 STS 提供刷新令牌，如果未吊销用户对应用的访问权限，它将返回一个新的访问令牌和一个新的刷新令牌。 这就是如何处理离开企业的用户的方案。 当 STS 收到刷新令牌时，如果用户不再获得授权，则不会发出其他有效的访问令牌。

## <a name="application-model"></a>应用程序模型

应用程序可以自行登录或将登录委托给标识提供者。 若要了解 Azure AD 支持的登录方案，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。

对于标识提供者，若要知道用户有权访问特定的应用，则必须向标识提供者注册用户和应用程序。 向 Azure AD 注册应用程序时，将为应用程序提供标识配置，使其能够与 Azure AD 集成。 注册应用程序还允许：

- 在登录对话框中自定义应用程序的商标。 这一点很重要，因为这是用户对你的应用程序的首次体验。
- 决定是否只允许用户登录到你的组织。 这是单租户应用程序。 或者允许用户使用任何工作或学校帐户登录。 这是一个多租户应用程序。 你还可以允许个人 Microsoft 帐户，或来自 LinkedIn、Google 的社交帐户，等等。
- 请求范围权限。 例如，你可以请求 "用户读取" 作用域，该作用域授予读取已登录用户的配置文件的权限。
- 定义定义对 Web API 的访问权限的作用域。 通常情况下，当应用程序想要访问你的 API 时，它需要请求对你定义的作用域的权限。
- 与 Azure AD 共享机密，以便向 Azure AD 证明应用的标识。  这适用于应用是机密客户端应用程序的情况。 机密客户端应用程序是可以安全地保存凭据的应用程序。 它们要求使用受信任的后端服务器来存储凭据。

注册后，将为应用程序提供一个 GUID，应用程序会在请求令牌时与 Azure AD 共享该 GUID。 如果应用是机密客户端应用程序，它还会共享机密或公钥，具体取决于是否使用了证书或机密。

Microsoft 标识平台使用满足以下两个主要功能的模型来表示应用程序：

根据应用程序所支持的身份验证协议来识别应用程序，并提供身份验证所需的所有标识符、Url、机密和相关信息。
Microsoft 标识平台：

* 保存支持在运行时进行身份验证所需的所有数据。
* 保存所有数据以确定应用可能需要访问的资源，以及在什么情况下应满足给定请求。
* 提供基础结构，用于实现应用程序开发人员租户内的应用程序预配和任何其他 Azure AD 租户。
* 处理令牌请求时间期间的用户同意，并协助跨租户动态预配应用

"同意" 是资源所有者授权客户端应用程序，以代表资源所有者访问受保护资源的权限。 Microsoft 标识平台：

* 使用户和管理员能够动态地同意或拒绝应用以他们的名义访问资源。
* 使管理员能够最终决定允许执行哪些应用、哪些用户可以使用特定的应用，以及如何访问目录资源。

在 Microsoft 标识平台中，**应用程序对象**将应用程序描述为抽象实体。 在部署时，Microsoft 标识平台使用应用程序对象作为蓝图来创建**服务主体，该服务主体**表示目录或租户中的应用程序的具体实例。 服务主体定义应用可在特定目标目录中实际执行的操作、可以使用它的资源、它有权访问的资源等。 Microsoft 标识平台通过**同意**从应用程序对象创建服务主体。

下图显示了征得同意后经过简化的 Microsoft 标识平台预配流程。 它显示两个租户（A 和 B）。 租户 A 拥有该应用程序。 租户 B 通过服务主体实例化应用程序。  

![征得同意后经过简化的预配流程](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在此预配流程中：

1. 来自租户 B 的某个用户尝试使用该应用登录，授权终结点请求应用程序的令牌。
1. 获取并验证用户凭据以进行身份验证。
1. 系统将提示用户提供对租户 B 的访问权限。
1. Microsoft 标识平台使用租户 A 中的应用程序对象作为在租户 B 中创建服务主体的蓝图。
1. 用户收到请求的令牌。

可以为其他租户重复此过程。 租户 A 保留了应用（应用程序对象）的蓝图。 向应用授予许可的所有其他租户的用户和管理员可以通过每个租户中的相应服务主体对象控制允许其执行的操作。 有关详细信息，请参阅 [Microsoft 标识平台中的应用程序和服务主体对象](app-objects-and-service-principals.md)。

## <a name="web-app-sign-in-flow-with-azure-ad"></a>具有 Azure AD 的 Web 应用登录流

当用户在浏览器中导航到某个 web 应用时，将发生以下情况：

- Web 应用确定是否对用户进行了身份验证。
- 如果用户未经过身份验证，web 应用将委托给 Azure AD 以登录用户。 登录将符合组织的策略，这可能意味着要求用户输入其凭据，使用多重身份验证，或者根本不使用密码（例如，使用 Windows Hello）。
- 要求用户同意该客户端应用所需的访问权限。 这就是需要在 Azure AD 中注册客户端应用程序的原因，以便 Azure AD 可以传递表示用户同意的访问权限的令牌。

当用户成功通过身份验证后：

- Azure AD 将令牌发送到 web 应用。
- 保存与 Azure AD 的域关联的 cookie，其中包含浏览器的 cookie jar 中的用户标识。 下次应用使用浏览器导航到 Azure AD 授权终结点时，浏览器将显示该 cookie，这样用户就无需再次登录。 这也是实现 SSO 的方式。 Cookie 由 Azure AD 生成，只能通过 Azure AD 理解。
- 然后，web 应用会验证该令牌。 如果验证成功，web 应用会显示受保护的页面，并在浏览器的 cookie jar 中保存会话 cookie。 当用户导航到另一页时，web 应用知道用户是根据会话 cookie 进行身份验证的。

下面的序列图概述了这种交互：

![web 应用身份验证过程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 应用如何确定用户是否已通过身份验证

Web 应用开发人员可以指示是所有页面还是仅某些页面都要求身份验证。 例如，在 ASP.NET/ASP.NET Core 中，这是通过将 `[Authorize]` 特性添加到控制器操作来完成的。 

此属性会导致 ASP.NET 检查是否存在包含用户标识的会话 cookie。 如果 cookie 不存在，ASP.NET 会将身份验证重定向到指定的标识提供者。 如果 Azure AD 标识提供者，则 web 应用会将身份验证重定向到 https://login.microsoftonline.com ，这会显示登录对话框。

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Web 应用如何委托登录 Azure AD 和获取令牌

用户身份验证通过浏览器进行。 OpenID 协议使用标准 HTTP 协议消息。
- Web 应用将 HTTP 302 （重定向）发送到浏览器以使用 Azure AD。
- 对用户进行身份验证时，Azure AD 通过浏览器使用重定向将令牌发送到 web 应用。
- 重定向由 web 应用以重定向 URI 的形式提供。 此重定向 URI 已注册到 Azure AD 应用程序对象。 可能有多个重定向 Uri，因为应用程序可能会部署到多个 Url。 因此，web 应用还需要指定要使用的重定向 URi。
- Azure AD 验证 web 应用发送的重定向 URI 是否是应用的已注册重定向 Uri 之一。

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>桌面和移动应用登录流与 Azure AD

上面所述的流适用于桌面和移动应用程序，但略有不同。

桌面和移动应用程序可使用嵌入的 Web 控件或系统浏览器进行身份验证。 下图显示桌面或移动应用如何使用 Microsoft 身份验证库（MSAL）获取访问令牌和调用 web Api。

![桌面应用程序的外观](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用浏览器获取令牌，对于 web 应用，将身份验证委托给 Azure AD。

由于 Azure AD 在浏览器中保存与 web 应用相同的标识 cookie，因此，如果本机应用程序或移动应用程序使用系统浏览器，则它会立即获取与相应 web 应用的 SSO。

默认情况下，MSAL 使用系统浏览器（.NET Framework 桌面应用程序除外），其中使用嵌入控件提供更集成的用户体验。

## <a name="next-steps"></a>后续步骤

- 请参阅[Microsoft 标识平台开发人员术语表](developer-glossary.md)，了解常见术语。
- 若要详细了解 Microsoft 标识平台支持的用户，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。
- 请参阅[MSAL 库](msal-overview.md)，了解 microsoft 库，这些库可帮助你开发使用 microsoft 帐户、Azure AD 帐户和 Azure AD B2C 用户在单个简化编程模型中的应用程序。
- 若要了解如何为应用服务应用配置身份验证，请参阅将[应用服务与 Microsoft 标识平台集成](/azure/app-service/configure-authentication-provider-aad)。
