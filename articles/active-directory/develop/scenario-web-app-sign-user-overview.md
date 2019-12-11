---
title: 从 Web 应用登录用户-Microsoft 标识平台 |Microsoft
description: 了解如何生成用于登录用户的 web 应用（概述）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a53f88a97cdba8626c9d6c421093e4ec327e19
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961601"
---
# <a name="scenario-web-app-that-signs-in-users"></a>方案：登录用户的 Web 应用

了解生成使用 Microsoft 标识平台登录用户所需的所有信息。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

若要创建登录用户的第一个可移植（ASP.NET Core） web 应用，请按照以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：登录用户 ASP.NET Core web 应用](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

若要了解如何将登录添加到现有的 ASP.NET web 应用程序，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：登录用户的 ASP.NET web 应用](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

如果你是 Java 开发人员，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：向 Java web 应用添加 Microsoft 登录](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

如果通过 Python 进行开发，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：向 Python web 应用添加 Microsoft 登录](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>概述

向 web 应用添加身份验证，以便它可以登录用户。 通过添加身份验证，web 应用可以访问有限的配置文件信息，以自定义用户体验。 

Web 应用在 web 浏览器中对用户进行身份验证。 在此方案中，web 应用将指示用户的浏览器登录到 Azure Active Directory （Azure AD）。 Azure AD 通过用户的浏览器返回一个登录响应，该浏览器在安全令牌中包含有关用户的声明。 用户登录时，将利用[OPEN ID Connect](./v2-protocols-oidc.md)标准协议，通过使用中间件[库](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)简化。

![通过 Web 应用让用户登录](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

作为第二个阶段，你可以让应用程序代表已登录用户调用 web Api。 下一阶段是一个不同的方案，你会在[用于调用 Web api 的 web 应用](scenario-web-app-call-api-overview.md)中找到该方案。

> [!NOTE]
> 将登录添加到 web 应用的目的是保护 web 应用并验证用户令牌，这是**中间件**库的用途。 对于 .NET，此方案还不需要 Microsoft 身份验证库（MSAL），这是为了获取令牌来调用受保护的 Api。 当 web 应用需要调用 web Api 时，将在跟进方案中引入身份验证库。

## <a name="specifics"></a>细节

- 在应用程序注册过程中，你需要提供一个或多个（如果你将应用程序部署到多个位置）的回复 Uri。 在某些情况下（ASP.NET 和 ASP.NET Core），需要启用 ID 令牌。 最后，你将需要设置注销 URI，使你的应用程序向用户注销。
- 在应用程序的代码中，需要提供 web 应用委托登录的权限。 你可能需要自定义令牌验证（特别是在合作伙伴方案中）。
- Web 应用程序支持任何帐户类型。 有关详细信息，请参阅[支持的帐户类型](v2-supported-account-types.md)。

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
