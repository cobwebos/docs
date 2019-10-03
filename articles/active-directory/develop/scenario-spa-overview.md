---
title: JavaScript 单页应用程序方案概述 - Microsoft 标识平台
description: 了解如何构建集成 Microsoft 标识平台的单页应用程序（方案概述）。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852516"
---
# <a name="scenario-single-page-application"></a>方案:单页应用程序

了解生成单页应用程序 (SPA) 所需的一切。

## <a name="prerequisites"></a>系统必备

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>开始使用

可以按 JavaScript SPA 快速入门创建第一个应用程序：

> [!div class="nextstepaction"]
> [快速入门：单页应用程序](./quickstart-v2-javascript.md)

## <a name="overview"></a>概述

许多新式 Web 应用程序都是使用 Angular、Vue.js 和 React.js 等 JavaScript 或 SPA 框架编写的客户端单页应用程序。 这些应用程序在 Web 浏览器中运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。 Microsoft 标识平台可让单页应用程序使用 [OAuth 2.0 隐式流](./v2-oauth2-implicit-grant-flow.md)将用户登录，并获取用于访问后端服务或 Web API 的令牌。 隐式流允许应用程序获取 ID 令牌来表示已经过身份验证的用户以及调用受保护 API 所需的访问令牌。

![单页应用程序](./media/scenarios/spa-app.svg)

此身份验证流不包括使用 Electron、React-Native 之类的跨平台 JavaScript 框架的应用程序方案。 因为它们需要使用其他功能才能与本机平台交互。

## <a name="specifics"></a>详情

若要为应用程序启用此方案，以下方面是必需的：

* 通过 Azure AD 进行应用程序注册涉及启用隐式流并设置重定向 URI，方便令牌返回。
* 带有注册的应用程序属性（例如应用程序 ID）的应用程序配置。
* 使用 MSAL 库完成身份验证流，以便登录并获取令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-spa-app-registration.md)
