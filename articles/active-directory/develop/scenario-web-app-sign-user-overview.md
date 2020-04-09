---
title: 从 Web 应用登录用户 - 微软身份平台 |蔚蓝
description: 了解如何构建用于登录用户的 Web 应用（概述）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881479"
---
# <a name="scenario-web-app-that-signs-in-users"></a>方案：在用户中签名的 Web 应用

了解生成一个可以通过 Microsoft 标识平台登录用户的 Web 应用所需的一切。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

如果需要创建第一个可移植 (ASP.NET Core) Web 应用来登录用户，请按以下快速入门操作：

> [!div class="nextstepaction"]
> [快速入门：ASP.NET核心 Web 应用程序，在用户中签名](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

如果想了解如何向现有 ASP.NET Web 应用程序添加登录，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：ASP.NET在用户中签名的 Web 应用](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

如果是 Java 开发人员，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：将 Microsoft 的登录添加到 Java Web 应用](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

如果使用 Python 进行开发，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [快速入门：将 Microsoft 登录添加到 Python Web 应用](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>概述

向 Web 应用添加身份验证，使之可以登录用户。 添加身份验证以后，Web 应用即可访问受限制的配置文件信息，以便自定义用户的体验。 

Web 应用可对 Web 浏览器中的用户进行身份验证。 在此方案中，Web 应用指示用户的浏览器让用户登录到 Azure Active Directory (Azure AD)。 Azure AD 通过用户的浏览器返回一个登录响应，该响应在一个安全令牌中包含了关于用户的声明。 登录用户会利用 [Open ID Connect](./v2-protocols-oidc.md) 标准协议，该协议已通过使用中间件[库](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)简化。

![通过 Web 应用让用户登录](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

在第二阶段，可让应用程序代表已登录用户调用 Web API。 此下一阶段是另一方案，详见[调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。

> [!NOTE]
> 向 Web 应用添加登录就是保护 Web 应用并验证用户令牌，这正是**中间件**库所做的。 就 .NET 而言，此方案尚不需要 Microsoft 身份验证库 (MSAL)，后者用于获取令牌来调用受保护的 API。 身份验证库在后续方案中引入，此时 Web 应用需要调用 Web API。

## <a name="specifics"></a>详情

- 在应用程序注册期间，需提供一个回复 URI，或多个回复 URI（如果将应用部署到多个位置）。 在某些情况下 (ASP.NET 和 ASP.NET Core)，需启用 ID 令牌。 最后需设置注销 URI，方便应用程序响应用户注销。
- 在应用程序代码中，需提供方便 Web 应用向其委托登录的机构。 可能需要自定义令牌验证（尤其是在合作伙伴方案中）。
- Web 应用程序支持任何帐户类型。 有关详细信息，请参阅[支持的帐户类型](v2-supported-account-types.md)。

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
