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
ms.openlocfilehash: 9c6edd0b3cfd6620f04553f9f6dfe89f1c7b7024
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536193"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>适用于 Python 的 MSAL 中的自定义令牌缓存序列化

在 MSAL Python 中，创建 [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) 的实例时，默认会提供一个内存中令牌缓存，该缓存的留存期与应用会话的持续时间相同。

将令牌缓存序列化，使应用的不同会话都可以访问它的功能不是现成提供的。 这是因为，MSAL Python 可以在无法访问文件系统的应用类型（例如 Web 应用）中使用。 若要在 MSAL Python 应用中使用持久性的令牌缓存，必须提供自定义令牌缓存序列化。

序列化令牌缓存的策略会有所不同，这取决于你是要编写公共客户端应用程序（桌面），还是机密客户端应用程序（web 应用、web API 或后台程序应用）。

## <a name="token-cache-for-a-public-client-application"></a>公共客户端应用程序的令牌缓存

公共客户端应用程序在用户的设备上运行，管理单个用户的令牌。 对于这种情况，可将整个缓存序列化成一个文件。 如果你的应用和另一应用可以同时访问缓存，请记得提供文件锁定。 [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) 类参考文档中提供的一个简单示例演示了如何在不锁定的情况下将令牌缓存序列化成文件。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 应用（机密客户端应用程序）的令牌缓存

对于 web 应用或 web Api，你可以使用会话、Redis 缓存或数据库来存储令牌缓存。 每个用户（每个帐户）应有一个令牌缓存，因此请确保按帐户序列化令牌缓存。

## <a name="next-steps"></a>后续步骤

有关如何使用 Windows 或 Linux Web 应用或 web API 的令牌缓存的示例，请参阅[webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 。 该示例适用于调用 Microsoft Graph API 的 Web 应用。
