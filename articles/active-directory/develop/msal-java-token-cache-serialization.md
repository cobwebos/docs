---
title: '自定义令牌缓存序列化 (MSAL4j) '
titleSuffix: Microsoft identity platform
description: 了解如何序列化 MSAL for Java 的令牌缓存
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312361"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>MSAL for Java 中的自定义令牌缓存序列化

若要在应用程序实例之间始终使用令牌缓存，需要自定义序列化。 涉及到令牌缓存序列化的 Java 类和接口如下所示：

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)：表示安全令牌缓存的接口。
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html)：接口，表示在访问前和访问后对执行代码进行的操作。 可以 @Override *beforeCacheAccess* 和 *afterCacheAccess*，让逻辑负责缓存的序列化和反序列化。
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html)：接口，表示访问令牌缓存时所在的上下文。 

下面是对令牌缓存序列化/反序列化进行的自定义序列化的简单实现 请勿将它复制并粘贴到生产环境中。

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

## <a name="learn-more"></a>了解详细信息

了解如何[使用 MSAL for Java 在令牌缓存中获取和删除帐户](msal-java-get-remove-accounts-token-cache.md)。
