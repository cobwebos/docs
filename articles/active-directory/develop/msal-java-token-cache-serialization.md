---
title: 自定义令牌缓存序列化（MSAL4j）
titleSuffix: Microsoft identity platform
description: 了解如何序列化 MSAL for Java 的令牌缓存
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d80011d3fbf8973ce913ac885a7841fe19760c4b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424302"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>MSAL for Java 中的自定义令牌缓存序列化

若要在你的应用程序的实例之间保存令牌缓存，你将需要自定义序列化。 标记缓存序列化中涉及的 Java 类和接口如下所示：

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)：代表安全令牌缓存的接口。
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html)：表示执行访问前后代码的操作的接口。 你应 @Override *beforeCacheAccess*和*afterCacheAccess*与用于序列化和反序列化缓存的逻辑。
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html)：表示访问令牌缓存的上下文的接口。 

下面是对令牌缓存序列化/反序列化的自定义序列化的简单实现。 不要将其复制并粘贴到生产环境中。

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>了解更多

了解如何[使用 MSAL For Java 从令牌缓存获取和删除帐户](msal-java-get-remove-accounts-token-cache.md)。
