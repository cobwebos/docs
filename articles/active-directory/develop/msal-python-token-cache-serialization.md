---
title: 自定义令牌缓存序列化 (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何序列化适用于 Python 的 MSAL 的令牌缓存
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846184"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>适用于 Python 的 MSAL 中的自定义令牌缓存序列化

在 MSAL Python 中，创建 [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) 的实例时，默认会提供一个内存中令牌缓存，该缓存的留存期与应用会话的持续时间相同。

将令牌缓存序列化，使应用的不同会话都可以访问它的功能不是现成提供的。 这是因为，MSAL Python 可以在无法访问文件系统的应用类型（例如 Web 应用）中使用。 若要在 MSAL Python 应用中使用持久性的令牌缓存，必须提供自定义令牌缓存序列化。

用于序列化令牌缓存的策略各不相同，具体取决于是编写公共客户端应用程序（桌面）还是编写机密客户端应用程序（Web 应用、Web API 或守护程序应用）。

## <a name="token-cache-for-a-public-client-application"></a>公共客户端应用程序的令牌缓存

公共客户端应用程序在用户的设备上运行，管理单个用户的令牌。 对于这种情况，可将整个缓存序列化成一个文件。 如果你的应用和另一应用可以同时访问缓存，请记得提供文件锁定。 [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) 类参考文档中提供的一个简单示例演示了如何在不锁定的情况下将令牌缓存序列化成文件。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 应用（机密客户端应用程序）的令牌缓存

对于 Web 应用或 Web API，可以使用会话、Redis 缓存或数据库来存储令牌缓存。 每个用户（每个帐户）应有一个令牌缓存，因此请确保按帐户序列化令牌缓存。

## <a name="next-steps"></a>后续步骤

有关如何使用 Windows/Linux Web 应用或 Web API 的令牌缓存的示例，请参阅 [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72)。 该示例适用于调用 Microsoft Graph API 的 Web 应用。
