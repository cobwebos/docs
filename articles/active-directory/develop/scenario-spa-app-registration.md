---
title: 注册单页应用程序 (SPA) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成单页应用程序（应用注册）
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9dc5b446e2ab26ca43c2a300e1af1237353325a3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682391"
---
# <a name="single-page-application-app-registration"></a>单页应用程序：应用注册

要在 Microsoft 标识平台中注册单页应用程序 (SPA)，请完成以下步骤。 注册步骤在支持隐式授权流的 MSAL 1.0 和支持使用 PKCE 的授权代码流的 MSAL 2.0 之间有所不同。

## <a name="create-the-app-registration"></a>创建应用注册

对于基于 MSAL 1.0 和 2.0 的应用程序，请先完成以下步骤以创建初始应用注册。

1. 登录 [Azure 门户](https://portal.azure.com)。 如果你的帐户有权访问多个租户，请在顶部菜单中选择“目录 + 订阅”筛选器，然后选择应包含你要创建的应用注册的租户。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册”。 
1. 选择“新注册”，输入应用程序的“名称”，并为应用程序选择“支持的帐户类型”。 请勿输入“重定向 URI”。 有关不同帐户类型的说明，请参阅 [使用 Azure 门户注册新应用程序](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)。
1. 选择“注册”以创建应用注册。

接下来，使用“重定向 URI”配置应用注册，以指定 Microsoft 标识平台应将客户端与任何安全令牌重定向到的位置。 按照适用于将在应用程序中使用的 MSAL.js 版本的步骤操作：

- [采用身份验证代码流的 MSAL.js 2.0](#redirect-uri-msaljs-20-with-auth-code-flow)（推荐）
- [采用隐式流的 MSAL.js 1.0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>重定向 URI：采用身份验证代码流的 MSAL.js 2.0

按照以下步骤为使用 MSAL.js 2.0 或更高版本的应用添加重定向 URI。 MSAL.js 2.0+ 支持使用 PKCE 和 CORS 的授权代码流，以响应[浏览器第三方 cookie 限制](reference-third-party-cookies-spas.md)。 MSAL.js 2.0+ 不支持隐式授权流。

1. 在 Azure 门户中，选择先前在[创建应用注册](#create-the-app-registration)中创建的应用注册。
1. 在“管理”下，选择“身份验证”，然后选择“添加平台”。
1. 在“Web 应用程序”下，选择“单页应用程序”磁贴。
1. 在“重定向 URI”下，输入 [重定向 URI](reply-url.md)。 请勿选中“隐式授权”下的任一复选框。
1. 选择“配置”以添加重定向 URI。

现在，你已经完成了单页应用程序 (SPA) 的注册，并配置了重定向 URI，客户端将被重定向到该 URI，并且安全令牌也将发送到该 URI。 通过使用“添加平台”窗格中的“单页应用程序”磁贴来配置重定向 URI，可以将应用程序注册配置为支持使用 PKCE 和 CORS 的授权代码流。

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>重定向 URI：采用隐式流的 MSAL.js 1.0

请按照以下步骤为使用 MSAL.js 1.3 或更早版本的单页应用和隐式授权流添加重定向 URI。 使用 MSAL.js 1.3 或更早版本的应用程序不支持身份验证代码流。

1. 在 Azure 门户中，选择先前在[创建应用注册](#create-the-app-registration)中创建的应用注册。
1. 在“管理”下，选择“身份验证”，然后选择“添加平台”。
1. 在“Web 应用程序”下，选择“单页应用程序”磁贴。
1. 在“重定向 URI”下，输入 [重定向 URI](reply-url.md)。
1. 启用“隐式流”：
    - 如果你的应用程序登录了用户，请选择“ID 令牌”。
    - 如果你的应用程序也需调用受保护的 Web API，请选择“访问令牌”。 有关这些令牌类型的更多信息，请参阅 [ID 令牌](id-tokens.md)和[访问令牌](access-tokens.md)。
1. 选择“配置”以添加重定向 URI。

现在，你已经完成了单页应用程序 (SPA) 的注册，并配置了重定向 URI，客户端将被重定向到该 URI，并且安全令牌也将发送到该 URI。 通过选择“ID 令牌”和“访问令牌”中的一个或选择两个，即表示启用了隐式授权流。

## <a name="note-about-authorization-flows"></a>关于授权流的注意事项

默认情况下，使用单页应用程序平台配置创建的应用注册会启用授权代码流。 要利用此流，你的应用程序必须使用 MSAL.js 2.0 或更高版本。

如前所述，使用 MSAL.js 1.3 的单页应用程序仅限于隐式授权流。 当前的 [OAuth 2.0 最佳做法](v2-oauth2-auth-code-flow.md)建议为 SPA 使用授权代码流而非隐式流。 拥有使用期有限的刷新令牌还可以帮助你的应用适应 Safari ITP 之类的[新式浏览器 cookie 隐私限制](reference-third-party-cookies-spas.md)。

当应用注册代表的所有生产单页应用程序都使用 MSAL.js 2.0 和授权代码流时，请取消选中 Azure 门户中应用注册的“身份验证”窗格的隐式授权设置。 但是，如果启用（选中）隐式流，则使用 MSAL.js 1.x 和隐式流的应用程序可以继续运行。

## <a name="next-steps"></a>后续步骤

接下来，配置应用代码，以使用在先前步骤中创建的应用注册：

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-spa-app-configuration.md)
