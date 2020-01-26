---
title: 构建一个可调用 web Api 的 web 应用-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的 web 应用（概述）
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
ms.custom: aaddev
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758983"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>方案：调用 web Api 的 web 应用

了解如何构建一个 web 应用，该应用将用户登录到 Microsoft 标识平台，然后代表登录用户调用 web Api。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此方案假设您已经完成了以下方案：

> [!div class="nextstepaction"]
> [用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概述

向 web 应用添加身份验证，以便它可以登录用户，并代表登录用户调用 web API。

![用于调用 Web API 的 Web 应用](./media/scenario-webapp/web-app.svg)

用于调用 web Api 的 web 应用是机密客户端应用程序。
这就是他们使用 Azure Active Directory （Azure AD）注册机密（应用程序密码或证书）的原因。 此密钥在调用 Azure AD 时传入，以获取令牌。

## <a name="specifics"></a>细节

> [!NOTE]
> 将登录添加到 web 应用是关于保护 web 应用本身的信息。 这种保护是通过使用*中间件*库来实现的，而不是 Microsoft 身份验证库（MSAL）。 上述方案是[登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)，涵盖了该主题。
>
> 此方案介绍如何从 web 应用调用 web Api。 必须获取这些 web Api 的访问令牌。 若要获取这些令牌，请使用 MSAL 库获取这些令牌。

此方案的开发涉及以下具体任务：

- 在[应用程序注册](scenario-web-app-call-api-app-registration.md)过程中，必须提供要与 Azure AD 共享的答复 URI、机密或证书。 如果你将应用程序部署到多个位置，则将为每个位置提供此信息。
- 在应用程序注册过程中，[应用程序配置](scenario-web-app-call-api-app-configuration.md)必须提供与 Azure AD 共享的客户端凭据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web 应用：应用注册](scenario-web-app-call-api-app-registration.md)
