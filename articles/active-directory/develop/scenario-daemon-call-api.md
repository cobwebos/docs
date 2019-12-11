---
title: 从后台应用程序调用 web API-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的守护程序应用
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962604"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>用于调用 web Api 的后台应用程序-从应用程序调用 web API

守护程序应用可以从 .NET 后台程序应用程序调用 web API，或调用多个预先批准的 web Api。

## <a name="calling-a-web-api-daemon-application"></a>调用 web API 后台程序应用程序

下面介绍如何使用令牌来调用 API：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>调用多个 Api

对于后台应用程序，你调用的 web Api 需要预先批准。 守护程序应用不会有任何增量许可（无用户交互）。 租户管理员需要预先同意该应用程序和所有 API 权限。 如果要调用多个 Api，则每次调用 `AcquireTokenForClient`时，都需要为每个资源获取令牌。 MSAL 将使用应用程序令牌缓存，以避免不必要的服务调用。

## <a name="next-steps"></a>后续步骤

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [后台应用程序-移动到生产环境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [后台应用程序-移动到生产环境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [后台应用程序-移动到生产环境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
