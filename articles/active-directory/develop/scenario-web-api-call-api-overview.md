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
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965120"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>方案：调用 web Api 的 Web API

了解你需要构建一个可调用 web Api 的 web API。

## <a name="prerequisites"></a>必备组件

此方案是一种受保护的 web api，可用于调用 web Api，在 "保护 web API" 方案之上构建。 若要了解有关此基础方案的详细信息，请参阅 "[受保护的 WEB API-方案](scenario-protected-web-api-overview.md)"。

## <a name="overview"></a>概述

- 客户端（web、桌面、移动或单页应用程序）-不在下图中表示-调用受保护的 web API 并在其 "Authorization" Http 标头中提供 JWT 持有者令牌。
- 受保护的 web API 将验证令牌，并使用 MSAL `AcquireTokenOnBehalfOf` 方法来请求（从 Azure AD）其他令牌，使其自身可以代表用户调用另一个 web API （称为下游 web API）。
- 受保护的 web API 使用此令牌来调用下游 API。 它还可以稍后调用 `AcquireTokenSilent`来请求其他下游 Api （但仍代表相同用户）的令牌。 `AcquireTokenSilent` 在需要时刷新该令牌。

![用于调用 web API 的 web API](media/scenarios/web-api.svg)

## <a name="specifics"></a>细节

与 API 权限相关的应用注册部分为经典。 应用程序配置涉及到使用 OAuth 2.0 代理流将 JWT 持有者令牌与下游 API 的令牌交换。 此令牌将添加到令牌缓存，在 web API 的控制器中提供此令牌，并可无提示地获取调用下游 Api 的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-web-api-call-api-app-registration.md)
