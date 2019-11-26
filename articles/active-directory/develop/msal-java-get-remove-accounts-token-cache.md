---
title: 使用 MSAL for Java 从令牌缓存获取和删除帐户（MSAL4j）
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 Java 的 Microsoft 身份验证库查看和删除令牌缓存中的帐户。
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452484"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>使用 MSAL for Java 从令牌缓存获取和删除帐户

MSAL for Java 默认提供内存中令牌缓存。 内存中令牌缓存持续时间为应用程序实例。

## <a name="see-which-accounts-are-in-the-cache"></a>查看哪些帐户在缓存中

可以通过调用 `PublicClientApplication.getAccounts()` 来检查缓存中的帐户，如以下示例中所示：

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>从缓存中删除帐户

若要从缓存中删除帐户，请查找需要删除的帐户，然后调用 `PublicClientApplicatoin.removeAccount()`，如以下示例中所示：

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>了解详细信息

如果使用 MSAL for Java，请参阅[MSAL For java 中的自定义令牌缓存序列化](msal-java-token-cache-serialization.md)。
