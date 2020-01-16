---
title: 构建一个可调用 web Api 的 web API-Microsoft 标识平台 |Microsoft
description: 了解如何构建调用下游 web Api （概述）的 web API。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b67507daf8005f3f9a299b778f1fba4ce67d46d4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044162"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>方案：调用 web Api 的 web API

了解生成用于调用 web Api 的 web API 需要了解的知识。

## <a name="prerequisites"></a>必备组件

这种情况下，受保护的 web API 可调用 web Api，这种方案在 "保护 web API" 方案之上构建。 若要了解有关此基础方案的详细信息，请参阅[方案：受保护的 WEB API](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>概述

- Web、桌面、移动或单页应用程序客户端（未在随附的关系图中表示）会调用受保护的 web API，并在其 "Authorization" HTTP 标头中提供一个 JSON Web 令牌（JWT）持有者令牌。
- 受保护的 web API 将验证令牌，并使用 Microsoft 身份验证库（MSAL） `AcquireTokenOnBehalfOf` 方法来请求 Azure Active Directory （Azure AD）中的另一个令牌，以使受保护的 web API 可以代表用户调用第二个 web API 或下游 web API。
- 受保护的 web API 还可以稍后调用 `AcquireTokenSilent`来代表同一个用户请求其他下游 Api 的令牌。 `AcquireTokenSilent` 在需要时刷新该令牌。

![用于调用 web API 的 web API 示意图](media/scenarios/web-api.svg)

## <a name="specifics"></a>细节

与 API 权限相关的 "应用注册" 部分为 "经典"。 应用配置涉及到使用 OAuth 2.0 代理流将 JWT 持有者令牌与下游 API 的令牌交换。 此令牌将添加到令牌缓存，在 web API 的控制器中提供此令牌，然后它可以无提示地获取调用下游 Api 的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-web-api-call-api-app-registration.md)
