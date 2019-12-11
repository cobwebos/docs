---
title: 注册一个用于调用 web Api 的 web 应用-Microsoft 标识平台 |Microsoft
description: 了解如何注册用于调用 web Api 的 web 应用
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
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962927"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>用于调用 web Api 的 web 应用-应用注册

Web 应用调用 web Api 与 Web 应用登录用户具有相同的注册。 因此，你需要遵循 Web 应用中登录[用户-应用注册](scenario-web-app-sign-user-app-registration.md)的说明

但是，由于 Web 应用现在调用 web Api，因此它将成为机密客户端应用程序。 这就是需要进行一些额外注册的原因：它需要与 Microsoft 标识平台共享机密（客户端凭据）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 权限

Web 应用程序代表已登录用户调用 Api。 它们需要请求委托的权限。 有关详细信息，请参阅[添加访问 Web api 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序的代码配置](scenario-web-app-call-api-app-configuration.md)
