---
title: 构建调用 Web API 的 Web API - Microsoft 标识平台 | Azure
description: 了解如何构建调用下游 Web API 的 Web API（概述）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4dcf377797709b56b4db735dabf4d48cfae4fc06
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257157"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>方案：调用 Web API 的 Web API

了解构建调用 Web API 的 Web API 所需的一切。

## <a name="prerequisites"></a>先决条件

这种情况下，受保护的 web API 会调用其他 web Api，这种情况在 [方案：受保护的 WEB api](scenario-protected-web-api-overview.md)上构建。

## <a name="overview"></a>概述

- Web、桌面、移动或单页应用程序客户端（附图中未提供）调用受保护的 Web API 并在其“Authorization”HTTP 标头中提供 JSON Web 令牌 (JWT) 持有者令牌。
- 受保护的 Web API 将验证该令牌并使用 Microsoft 身份验证库 (MSAL) `AcquireTokenOnBehalfOf` 方法来从 Azure Active Directory (Azure AD) 请求另一个令牌，以便受保护的 Web API 可以代表用户调用另一个 Web API 或下游 Web API。
- 受保护的 Web API 稍后也可以代表同一用户调用 `AcquireTokenSilent` 来请求其他下游 API 的令牌。 `AcquireTokenSilent` 在需要时刷新令牌。

![调用 Web API 的 Web API 的图示](media/scenarios/web-api.svg)

## <a name="specifics"></a>详情

与 API 权限相关的应用注册部分很经典。 应用配置涉及使用 OAuth 2.0 代理流将 JWT 持有者令牌与下游 API 的令牌进行交换。 此令牌会添加到令牌缓存中，在缓存中可供 Web API 的控制器使用，然后它可以在无提示的情况下获取令牌来调用下游 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-web-api-call-api-app-registration.md)
