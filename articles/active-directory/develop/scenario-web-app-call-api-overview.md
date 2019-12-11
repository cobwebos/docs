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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962128"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>方案：调用 web Api 的 Web 应用

了解如何构建一个 web 应用，用于在 Microsoft 标识平台上登录用户，然后代表已登录用户调用 web Api。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

在这种情况下，假设您已经完成了以下方案：

> [!div class="nextstepaction"]
> [登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概述

向 Web 应用添加身份验证，这可以让用户登录，并代表登录用户调用 Web API。

![用于调用 Web API 的 Web 应用](./media/scenario-webapp/web-app.svg)

用于调用 web Api 的 web 应用：

- 是机密客户端应用程序。
- 这就是他们使用 Azure AD 注册了机密（应用程序密码或证书）的原因。 此密钥在调用 Azure AD 时传入，以获取令牌

## <a name="specifics"></a>细节

> [!NOTE]
> 将登录添加到 Web 应用不会使用 MSAL 库，因为这是为了保护 Web 应用。 保护库是通过名为中间件的库实现的。 这是上一个方案[登录用户到 Web 应用](scenario-web-app-sign-user-overview.md)的对象
>
> 从 Web 应用调用 web Api 时，需要获取这些 web Api 的访问令牌。 可以使用 MSAL 库获取这些令牌。

因此，开发人员对此方案的端到端体验具有以下特点：

- 在[应用程序注册](scenario-web-app-call-api-app-registration.md)过程中，你需要提供一个或多个（如果你将应用程序部署到多个位置），则需要与 Azure AD 共享回复 uri、机密或证书。
- 在应用程序注册过程中，[应用程序配置](scenario-web-app-call-api-app-configuration.md)需要提供与 Azure AD 共享的客户端凭据

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-web-app-call-api-app-registration.md)
