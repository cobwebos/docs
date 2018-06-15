---
title: 什么是 Azure Active Directory B2C？ | Microsoft Docs
description: 了解如何使用 Azure Active Directory B2C 创建和管理应用程序登录体验。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 878d4da715308abf6dbe536ea0d1df2a68225179
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711308"
---
# <a name="what-is-azure-active-directory-b2c"></a>什么是 Azure Active Directory B2C？

Azure Active Directory (Azure AD) B2C 是一项标识管理服务，用于自定义和控制客户在使用应用程序时的注册、登录和管理配置文件的方式。 此类应用程序包括为 iOS、Android、.NET 等系统开发的应用程序。 使用 Azure AD B2C，可以在执行这些操作的同时保护客户标识。

可以配置已注册到 Azure AD B2C 的应用程序，以执行各种标识管理操作。 下面是一些示例：

- 让客户注册，以使用注册的应用程序
- 让注册的客户登录，并开始使用你的应用程序
- 让注册的客户编辑其个人资料
- 在应用程序中启用多重身份验证
- 让客户使用特定的标识提供程序注册和登录
- 授予从你的应用程序访问生成的 API 的权限
- 自定义注册和登录体验的外观
- 管理应用程序的单一登录会话

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>在使用 Azure AD B2C 之前需要考虑哪些因素？

- 希望客户如何与应用程序交互？
- 要向客户提供哪种用户界面 (UI) 体验？
- 允许客户在应用程序中选择哪些标识提供程序？
- 登录过程是否要求运行其他 API？

### <a name="customer-interaction"></a>客户交互

Azure AD B2C 支持对所有客户体验使用 [OpenID Connect](https://openid.net/connect/)。 在 OpenID Connect 的 Azure AD B2C 实现中，应用程序通过向 Azure AD B2C 发出身份验证请求，来发起此用户旅程。 请求的结果是 `id_token`。 此安全令牌代表客户的标识。

必须通过 Azure 门户将使用 Azure AD B2C 的每个应用程序注册到 Azure AD B2C 租户中。 注册过程将收集一些值并将其分配给应用程序。 这些值包括用于唯一标识应用程序的应用程序 ID，以及可用于将响应定向回到该应用程序的重定向 URI。

每个应用程序的交互遵循类似的高级模式：

1. 应用程序指示客户运行某个策略。
2. 客户根据策略定义完成该策略。
3. 应用程序接收安全令牌。
4. 应用程序使用该安全令牌尝试访问受保护的资源。
5. 资源服务器验证安全令牌，确认是否可以授予访问权限。
6. 应用程序定期刷新安全令牌。

根据要生成的应用程序类型，这些步骤可能稍有不同。

Azure AD B2C 依次与标识提供程序、客户、其他系统和本地目录交互，以完成标识任务。 例如，登录客户、注册新客户或重置密码。 建立多方信任和完成这些步骤的底层平台称为“标识体验框架”。 此框架和某个策略（也称为用户旅程或信任框架策略）显式定义执行组件、操作、协议和要完成的步骤顺序。

Azure AD B2C 通过多种方式防范应用程序遭受拒绝服务和密码攻击。 Azure AD B2C 使用检测和缓解技术（例如 SYN Cookie、速率和连接限制）来防范资源遭受拒绝服务攻击。 此外，还包括针对暴力破解密码攻击和字典密码攻击的缓解措施。

#### <a name="built-in-policies"></a>内置策略

发送到 Azure AD B2C 的每个请求都指定了一个策略。 策略控制应用程序与 Azure AD B2C 的交互方式。 已针对注册、登录和配置文件编辑等最常见的标识任务预定义了内置策略。  例如，注册策略允许通过配置以下设置来控制行为：

- 客户可用来注册应用程序的社交帐户
- 从客户收集的数据，例如名字或邮政编码
- 多重身份验证
- 所有注册页面的外观
- 返回给应用程序的信息

#### <a name="custom-policies"></a>自定义策略 

[自定义策略](active-directory-b2c-overview-custom.md)是在 Azure AD B2C 租户中定义标识体验框架行为的配置文件。 可以全面编辑自定义策略以完成大量任务。 自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 

可根据需要在 Azure AD B2C 租户中使用不同类型的多个自定义策略，并可以在不同的应用程序之间重复使用这些策略。 由于这种灵活性，我们只需对代码做出极少量的更改或根本不需要更改，即可定义和修改客户标识体验。 可以通过在 HTTP 身份验证请求中添加特殊的查询参数来使用策略。

可通过以下方式使用自定义策略来控制用户旅程：

- 定义与 API 的交互方式以捕获其他信息、验证客户提供的声明，或触发外部进程。
- 基于 API 或者目录中声明内的声明（例如 *migrationStatus*）更改行为。
- 内置策略未涵盖的任何工作流。 例如，在使用登录体验期间从客户收集更多信息，以及执行授权检查以访问资源。

### <a name="identity-providers"></a>标识提供者

标识提供者是验证用户身份和颁发安全令牌的服务。 在 Azure AD B2C 中，可以在租户内配置大量的标识提供者，例如 Microsoft 帐户、Facebook 或 Amazon 等等。 

若要在 Azure AD B2C 租户中配置标识提供者，必须从创建的标识提供者应用程序记录应用程序标识符或客户端标识符，以及密码或客户端机密。 然后，使用此标识符和密码来配置应用程序。

### <a name="user-interface-experience"></a>用户界面体验

可以控制呈现给客户的大部分 HTML 和 CSS 内容。 使用页面 UI 自定义功能可以自定义任何策略的外观。 还可以在应用程序与 Azure AD B2C 之间保持品牌和视觉一致性。

Azure AD B2C 在客户的浏览器中运行代码，并使用称为“跨域资源共享”(CORS) 的新式方法。 首先，在包含自定义 HTML 内容的策略中指定 URL。 Azure AD B2C 会将 UI 元素与从 URL 加载的 HTML 内容合并，然后向客户显示页面。

可以在查询字符串向 Azure AD B2C 发送参数。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Azure AD B2C 如何入门？

在 Azure AD B2C 中，租户表示组织，并且是用户的目录。 每个 Azure AD B2C 租户都是独特的，独立于其他 Azure AD B2C 租户。 租户包含已注册使用你的应用程序的客户的信息。 例如，密码、个人资料数据和权限。

需要将 Azure AD B2C 租户链接到 Azure 订阅以启用所有功能，并支付使用费。 若要允许 Azure AD B2C 客户登录你的应用程序，必须将该应用程序注册到 Azure AD B2C 租户中。

在将应用程序配置为使用 Azure AD B2C 之前，首先需要创建一个 Azure AD B2C 租户并注册该应用程序。 若要注册应用程序，请完成[教程：注册应用程序以便能够使用 Azure AD B2C 注册和登录](tutorial-register-applications.md)中的步骤。
  
如果你是 ASP.NET Web 应用程序开发人员，请使用[教程：使 Web 应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-web-app.md)中的步骤，将应用程序设置为对帐户进行身份验证。

如果你是桌面应用程序开发人员，请使用[教程：使桌面应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-desktop-app.md)中的步骤，将应用程序设置为对帐户进行身份验证。

如果你是使用 Node.js 的单页应用程序开发人员，请使用[教程：使单页应用程序能够使用 Azure AD B2C 进行帐户身份验证](active-directory-b2c-tutorials-spa.md)中的步骤，将应用程序设置为对帐户进行身份验证。

## <a name="next-steps"></a>后续步骤

请继续学习教程，开始在应用程序中配置注册和登录体验。

> [!div class="nextstepaction"]
> [教程：注册应用程序以便能够使用 Azure AD B2C 注册和登录](tutorial-register-applications.md)
