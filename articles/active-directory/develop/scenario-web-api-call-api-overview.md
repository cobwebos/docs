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
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885066"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>方案：调用 Web API 的 Web API

了解构建调用 Web API 的 Web API 所需的一切。

## <a name="prerequisites"></a>先决条件

此方案（受保护的 Web API 调用 Web API）基于“保护 Web API”方案。 若要详细了解此基础方案，请参阅[方案：受保护的 Web API](scenario-protected-web-api-overview.md)。

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
