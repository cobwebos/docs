---
title: 构建调用 web API 的单页应用-Microsoft 标识平台 |Microsoft
description: 了解如何生成调用 web API 的单页应用程序
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
ms.openlocfilehash: f3cafb4d2df4d1944f583a99ca2014f3841827d0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701852"
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
