---
title: "Azure Active Directory B2C：应用程序注册 | Microsoft Docs"
description: "如何将应用程序注册到 Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: PatAltimore
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.openlocfilehash: b49a894365dd344afb2f3cab51e7cfd28d930b1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C：注册应用程序

此快速入门帮助你在数分钟内在 Microsoft Azure Active Directory (Azure AD) B2C 租户中注册一个应用程序。 完成后，应用程序处于已注册状态，可以在 Azure B2C 租户中使用了。

## <a name="prerequisites"></a>先决条件

若要构建可接受使用者注册和登录的应用程序，首先需要使用 Azure Active Directory B2C 租户注册应用程序。 通过使用 [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)中所述的步骤获取自己的租户。

从 Azure 门户中的 Azure AD B2C 边栏选项卡创建的应用程序必须从同一位置进行管理。 如果使用 PowerShell 或另一个门户编辑 B2C 应用程序，则应用程序不受支持且不适用于 Azure AD B2C。 有关详细信息，请参阅[出错的应用](#faulted-apps)部分。 

本文所用示例有助于示例入门。 可以在后续文章中详细了解这些示例。

## <a name="navigate-to-b2c-settings"></a>导航到 B2C 设置

以 B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>根据应用程序类型选择后续步骤

* [注册 Web 应用程序](#register-a-web-app)
* [注册 Web API](#register-a-web-api)
* [注册移动或本机应用程序](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>注册 Web 应用

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>创建 Web 应用客户端机密

如果 Web 应用程序调用受 Azure AD B2C 保护的 Web API，请执行以下步骤：
   1. 创建应用程序机密，方法是：转到“密钥”边栏选项卡，并单击“生成密钥”按钮。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。
   2. 单击“API 访问权限”，再单击“添加”，然后选择 Web API 和作用域（权限）。

> [!NOTE]
> **应用程序密钥** 是一个重要的安全凭据，应进行适当地保护。
> 

[跳转到“后续步骤”](#next-steps)

### <a name="register-a-web-api"></a>注册 Web API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

单击“已发布作用域”，根据需要添加更多作用域。 默认情况下，定义“user_impersonation”作用域。 有了 user_impersonation 作用域，其他应用程序就可以代表已登录用户访问该 API。 可以随意删除 user_impersonation 作用域。

[跳转到“后续步骤”](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>注册移动或本机应用

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[跳转到“后续步骤”](#next-steps)

## <a name="limitations"></a>限制

### <a name="choosing-a-web-app-or-api-reply-url"></a>选择 Web 应用或 API 回复 URL

使用 Azure AD B2C 注册的应用目前仅限使用一组有限的回复 URL 值。 Web 应用和服务的回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，无法注册具有以下回复 URL 的 Web 应用：

`https://login-east.contoso.com`

`https://login-west.contoso.com`

注册系统会将现有回复 URL 的完整 DNS 名称与要添加的回复 URL 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求会失败：

* 新回复 URL 的完整 DNS 名称与现有回复 URL 的 DNS 名称不匹配。
* 新回复 URL 的完整 DNS 名称不是现有回复 URL 的子域。

例如，如果应用具有以下回复 URL：

`https://login.contoso.com`

可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，将引用 login.contoso.com 的 DNS 子域。如果希望应用使用 login-east.contoso.com 和 login-west.contoso.com 作为回复 URL，必须按以下顺序添加这些回复 URL：

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

可以添加后两个回复 URL，因为它们是第一个回复 URL (contoso.com) 的子域。

### <a name="choosing-a-native-app-redirect-uri"></a>选择本机应用重定向 URI

为移动/本机应用程序选择重定向 URI 时，有两个重要的考虑事项：

* **唯一**：每个应用程序的重定向 URI 的方案应是唯一的。 在我们的示例 (com.onmicrosoft.contoso.appname://redirect/path) 中，我们使用 com.onmicrosoft.contoso.appname 作为方案。 建议遵循此模式。 如果两个应用程序共享同一方案，用户会看到“选择应用”对话框。 如果用户的选择不正确，登录会失败。
* **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域后包含至少一个正斜杠（例如，//contoso/ 可以使用，//contoso 不能使用）。

请确保重定向 URI 中没有下划线等特殊字符。

### <a name="faulted-apps"></a>出错的应用

不应按以下方式编辑 B2C 应用程序：

* 在其他应用程序管理门户上（如 [Azure 经典门户](https://manage.windowsazure.com/)和[应用程序注册门户](https://apps.dev.microsoft.com/)）编辑。
* 使用图形 API 或 PowerShell 编辑

如果按照上述方式编辑 B2C 应用程序，并尝试在 Azure 门户上的 Azure AD B2C 功能边栏选项卡中再次进行编辑，那么它将成为出错的应用，且应用程序将无法再用于 Azure AD B2C。 需删除应用程序并重新创建。

若要删除应用，请转到[应用程序注册门户](https://apps.dev.microsoft.com/)并删除该应用程序。 若要使应用程序可见，须为该应用程序的所有者（而不仅仅是租户管理员）。

## <a name="next-steps"></a>后续步骤

将应用程序注册到 Azure AD B2C 以后，接下来可以完成[快速入门教程](active-directory-b2c-overview.md#get-started)之一，进一步做好启动和运行准备。

> [!div class="nextstepaction"]
> [使用注册、登录和密码重置创建 ASP.NET Web 应用](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
