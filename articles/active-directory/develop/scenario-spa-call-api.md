---
title: 单页应用程序（调用 web API）-Microsoft 标识平台
description: 了解如何生成单页面应用程序（调用 web API）
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766151"
---
# <a name="single-page-application-call-a-web-api"></a>单页应用程序：调用 web API

建议调用 `acquireTokenSilent` 方法，以便在调用 web API 之前获取或续订访问令牌。 获得令牌后，可以调用受保护的 web API。

## <a name="call-a-web-api"></a>调用 Web API

### <a name="javascript"></a>JavaScript

使用获取的访问令牌作为 HTTP 请求中的持有者来调用任何 web API，例如 Microsoft Graph API。 例如：

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

### <a name="angular"></a>Angular

MSAL 角包装充分利用 HTTP 拦截器自动获取访问令牌，并将其附加到 Api 的 HTTP 请求。 有关详细信息，请参阅[获取用于调用 API 的令牌](scenario-spa-acquire-token.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到生产](scenario-spa-production.md)
