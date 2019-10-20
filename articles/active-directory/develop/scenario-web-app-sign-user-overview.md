---
title: 登录用户的 Web 应用（概述）-Microsoft 标识平台
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
ms.openlocfilehash: 68d47d4233aec62ec5f1955e52025b0d55221af8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596717"
---
# <a name="scenario-web-app-that-signs-in-users"></a>方案：登录用户的 Web 应用

了解使用 Microsoft 标识平台构建登录用户所需的所有信息。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

若要创建登录用户的第一个可移植（ASP.NET Core） web 应用，请按照以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：登录用户 ASP.NET Core web 应用](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

要了解如何将登录添加到旧的 ASP.NET web 应用程序，请尝试以下教程：

> [!div class="nextstepaction"]
> [快速入门：登录用户的 ASP.NET web 应用](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

如果你是 Java 开发人员，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：向 Java web 应用添加 Microsoft 登录](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

如果通过 Python 进行开发，请尝试执行以下操作：

> [!div class="nextstepaction"]
> [快速入门：向 Python web 应用添加 Microsoft 登录](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>概述

向 web 应用添加身份验证，以便它可以登录用户。 通过添加身份验证，web 应用可以访问有限的配置文件信息，例如，自定义你向其用户提供的体验。 Web 应用在 web 浏览器中对用户进行身份验证。 在此方案中，Web 应用程序指示用户的浏览器让用户登录到 Azure AD 中。 Azure AD 通过用户的浏览器返回一个登录响应，该响应在一个安全令牌中包含了关于用户的声明。 登录用户使用中间件[库](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)简化了[Open ID Connect](./v2-protocols-oidc.md)标准协议。

![Web 应用登录用户](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

作为第二个阶段，你还可以让应用程序代表已登录用户调用 Web Api。 下一阶段是一个不同的方案，你可以在[Web 应用调用 Web api](scenario-web-app-call-api-overview.md)中找到该方案

> [!NOTE]
> 将登录添加到 web 应用的目的是保护 web 应用，并验证用户令牌，这是**中间件**库的用途。 对于 .NET，此方案不需要 Microsoft 身份验证库（MSAL），这是为了获取令牌来调用受保护的 Api。 仅当 web 应用需要调用 web Api 时，才会在跟进方案中引入身份验证库。

## <a name="specifics"></a>细节

- 在应用程序注册过程中，你需要提供一个或多个（如果你将应用程序部署到多个位置）的回复 Uri。 在某些情况下（ASP.NET/ASP.NET Core），需要启用 ID 令牌。 最后，你将需要设置一个注销 URI，使你的应用程序向用户注销。
- 在应用程序的代码中，需要提供 web 应用委托登录的权限。 你可能需要自定义令牌验证（特别是在 ISV 方案中）。
- Web 应用程序支持任何帐户类型。 有关详细信息，请参阅[支持的帐户类型](v2-supported-account-types.md)。

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore?tabs=aspnetcore)

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
