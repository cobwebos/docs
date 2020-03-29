---
title: 注册调用 Web API 的 Web 应用 - Microsoft 标识平台 | Azure
description: 了解如何注册调用 Web API 的 Web 应用
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759051"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>调用 Web API 的 Web 应用：应用注册

调用 Web API 的 Web 应用与登录用户的 Web 应用具有相同的注册。 因此，请按照[在用户中签名的 Web 应用中的说明进行操作：应用注册](scenario-web-app-sign-user-app-registration.md)。

但是，由于 Web 应用现在也调用 Web API，因此它将成为一个机密客户端应用程序。 这就是需要额外注册的原因。 此应用必须与 Microsoft 标识平台共享客户端凭据或机密**。

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 权限

Web 应用代表已登录用户调用 API。 为此，它们必须请求委托的权限**。 有关详细信息，请参阅[添加访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web 应用：代码配置](scenario-web-app-call-api-app-configuration.md)
