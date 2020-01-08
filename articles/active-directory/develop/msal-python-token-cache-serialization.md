---
title: 自定义令牌缓存序列化（MSAL Python） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何序列化适用于 Python 的 MSAL 的令牌缓存
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5dc00f1aeb6b4fba987295ff5fc7dfd389b790
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424084"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>用于 Python 的 MSAL 中的自定义令牌缓存序列化

在 MSAL Python 中，默认情况下，当你创建[ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)的实例时，将默认提供内存中令牌缓存，该缓存在应用会话期间保持。

标记缓存的序列化，使应用程序的不同会话可以访问它，而不提供 "现成的"。 这是因为，可以在无法访问文件系统的应用程序类型（例如 Web 应用）中使用 MSAL Python。 若要在 MSAL Python 应用中使用永久性令牌缓存，必须提供自定义令牌缓存序列化。

序列化令牌缓存的策略会有所不同，这取决于你是要编写公共客户端应用程序（桌面），还是机密客户端应用程序（Web 应用、Web API 或后台程序应用）。

## <a name="token-cache-for-a-public-client-application"></a>公共客户端应用程序的令牌缓存

公共客户端应用程序在用户设备上运行，并管理单个用户的令牌。 在这种情况下，您可以将整个缓存序列化到一个文件中。 如果你的应用程序或其他应用程序可以同时访问缓存，请记住提供文件锁定。 有关如何在不锁定的情况下将令牌缓存序列化到文件的简单示例，请参阅[SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)类参考文档中的示例。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 应用的令牌缓存（机密客户端应用程序）

对于 Web 应用或 Web Api，你可以使用会话、Redis 缓存或数据库来存储令牌缓存。 每个用户应有一个令牌缓存（每个帐户），因此请确保序列化每个帐户的令牌缓存。

## <a name="next-steps"></a>后续步骤

有关如何使用 Windows 或 Linux Web 应用或 Web API 的令牌缓存的示例，请参阅[webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 。 该示例适用于调用 Microsoft Graph API 的 web 应用。
