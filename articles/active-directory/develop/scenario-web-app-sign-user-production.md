---
title: 将用于登录用户的 Web 应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何构建用于登录用户的 Web 应用（移到生产环境）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181624"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>用于登录用户的 Web 应用：移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，接下来学习如何将其移到生产环境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

### <a name="troubleshooting"></a>故障排除

> [!NOTE]
> 当用户首次登录到 web 应用程序时，他们将需要同意。 但是，在某些组织中，用户可以看到如下所示的消息：
>
> *AppName 需要具有访问组织中只有管理员才能授予的资源的权限。请让管理员向此应用授予权限，然后才能使用该应用。*
>
> 这是因为你的租户管理员已**禁止**用户同意。 在这种情况下，需要与租户管理员联系，以便他们对应用程序所需的范围进行管理员同意。

### <a name="same-site"></a>同一站点

请确保了解 Chrome 浏览器的新版本可能存在的问题

> [!div class="nextstepaction"]
> [如何在 Chrome 浏览器中处理 SameSite Cookie 更改](howto-handle-samesite-cookie-changes-chrome-browser.md)

SameSite NuGet 包处理最常见的问题。

### <a name="scenario-for-calling-web-apis"></a>用于调用 Web API 的方案

Web 应用登录用户后，它就可以代表已登录用户调用 Web API。 从 Web 应用调用 Web API 是以下方案的目标：

> [!div class="nextstepaction"]
> [调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探讨： ASP.NET Core web 应用教程

了解如何在此 ASP.NET Core 教程中通过其他方法登录用户： 

> [!div class="nextstepaction"]
> [允许 Web 应用通过 Microsoft 面向开发人员的标识平台登录用户和调用 API](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

此循序渐进教程提供了 Web 应用的生产就绪代码，包括如何在以下组织中添加使用帐户进行的登录：

- 你的组织
- 多个组织
- 工作或学校帐户或个人 Microsoft 帐户
- [Azure AD B2C](https://aka.ms/aadb2c)
- 国家/地区云

## <a name="sample-code-java-web-app"></a>示例代码： Java web 应用

通过 GitHub 上的此示例详细了解 Java Web 应用： 

> [!div class="nextstepaction"]
> [一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
