---
title: JavaScript 单页应用方案 - Microsoft 标识平台 | Azure
description: 了解如何使用 Microsoft 标识平台构建单页应用程序（方案概述）。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc0aee2e6c1b06850ffd0385626955a9798e7aeb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257310"
---
# <a name="scenario-single-page-application"></a>方案：单页应用程序

了解生成单页应用程序 (SPA) 所需的一切。

## <a name="getting-started"></a>入门

如果尚未这样做，请先完成 JavaScript SPA 快速入门：

[快速入门：单页应用程序](./quickstart-v2-javascript.md)

## <a name="overview"></a>概述

许多新式 Web 应用程序都是作为客户端单页应用程序构建的。 开发人员使用 JavaScript 或 SPA 框架（如 "角度"、"Vue" 和 "响应"）编写它们。 这些应用程序在 Web 浏览器上运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。

Microsoft 标识平台提供了 **两个** 选项，使单页应用程序可以登录用户并获取用于访问后端服务或 web api 的令牌：

- [具有 PKCE) 的 OAuth 2.0 授权代码流 (](./v2-oauth2-auth-code-flow.md)。 授权代码流允许应用程序交换 **ID** 令牌的授权代码，以表示调用受保护的 api 所需的经过身份验证的用户和 **访问** 令牌。 此外，它还会返回 **刷新** 令牌，以代表用户提供对资源的长期访问权限，而无需与这些用户交互。 这是 **建议** 的方法。

![单页应用程序-身份验证](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 隐式流](./v2-oauth2-implicit-grant-flow.md)。 隐式授权流允许应用程序获取 **ID** 和 **访问** 令牌。 与授权代码流不同，隐式授权流不返回 **刷新令牌**。

![单页应用程序-隐式](./media/scenarios/spa-app.svg)

此身份验证流不包括使用 Electron 和 React-Native 之类的跨平台 JavaScript 框架的应用程序方案。 它们需要使用其他功能才能与本机平台交互。

## <a name="specifics"></a>详情

若要为应用程序启用此方案，需要：

* 通过 Azure Active Directory (Azure AD) 进行应用程序注册。 在隐式授权流和授权代码流之间，注册步骤不同。
* 应用程序配置，其中包含已注册的应用程序属性，如应用程序 ID。
* 使用适用于 JavaScript 的 Microsoft 身份验证库 ( # A0) 执行身份验证流以登录和获取令牌。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-spa-app-registration.md)
