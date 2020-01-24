---
title: 注册一个用于调用 web Api 的 web API-Microsoft 标识平台 |Microsoft
description: 了解如何构建调用下游 web Api （应用注册）的 web API。
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701784"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>用于调用 web Api 的 web API：应用注册

调用下游 web api 的 web API 与受保护的 web API 具有相同的注册。 因此，需要按照[受保护的 WEB API：应用注册](scenario-protected-web-api-app-registration.md)中的说明进行操作。

由于 web 应用现在调用 web Api，因此它将成为机密客户端应用程序。 这就是需要额外注册信息的原因：应用需要与 Microsoft 标识平台共享机密（客户端凭据）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 权限

Web 应用代表接收了持有者令牌的用户调用 Api。 Web 应用需要请求委托的权限。 有关详细信息，请参阅[添加访问 Web api 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：代码配置](scenario-web-api-call-api-app-configuration.md)
