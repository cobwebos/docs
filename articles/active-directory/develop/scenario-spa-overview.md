---
title: JavaScript 单页应用方案-Microsoft 标识平台 |Microsoft
description: 了解如何使用 Microsoft 标识平台构建单页应用程序（方案概述）。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701869"
---
# <a name="scenario-single-page-application"></a>方案：单页应用程序

了解你需要构建单页应用程序（SPA）。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

可以按照 JavaScript SPA 快速入门中的步骤创建第一个应用程序：

> [!div class="nextstepaction"]
> [快速入门：单页应用程序](./quickstart-v2-javascript.md)

## <a name="overview"></a>概述

许多新式 web 应用程序都是作为客户端单页面应用程序构建的。 开发人员使用 JavaScript 或 SPA 框架（如角度、Vue 和反应）编写它们。 这些应用程序在 web 浏览器上运行，并具有不同于传统服务器端 web 应用程序的身份验证特征。 

Microsoft 标识平台使单页应用程序能够使用[OAuth 2.0 隐式流](./v2-oauth2-implicit-grant-flow.md)登录用户和获取令牌，以便访问后端服务或 web api。 隐式流允许应用程序获取 ID 令牌来表示经过身份验证的用户，还可以访问调用受保护的 Api 所需的令牌。

![单页应用程序](./media/scenarios/spa-app.svg)

此身份验证流不包括使用跨平台 JavaScript 框架（如 Electron 和响应本机）的应用程序方案。 它们需要更多的功能来与本机平台进行交互。

## <a name="specifics"></a>细节

若要为应用程序启用此方案，需要：

* 应用程序注册 Azure Active Directory （Azure AD）。 此注册涉及启用隐式流和将重定向 URI 设置为要返回的令牌。
* 应用程序配置，其中包含已注册的应用程序属性，如应用程序 ID。
* 使用 Microsoft 身份验证库（MSAL）执行身份验证流以登录并获取令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-spa-app-registration.md)
