---
title: Azure Key Vault 限制指南
description: Key Vault 限制可限制并发调用数，以防止过度使用资源。
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 限制指南

限制进程可在启动后，用于限定对 Azure 服务发出的并发调用数，以防止过度使用资源。 Azure Key Vault (AKV) 能够处理大量请求。 在请求数过多的情况下，限制客户端请求可帮助 AKV 服务保持最佳性能和可靠性。

限制值因情况而异。 例如，如果正在执行大量写入，则发生限制的可能性会比仅执行读取高。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何处理其限制？

Key Vault 中的服务限制用于防止资源滥用，确保所有 Key Vault 客户端的服务质量。 当超过服务阈值时，Key Vault 会在一段时间内限制该客户端发出其他任何请求。 在这种情况下，Key Vault 返回 HTTP 状态代码 429（请求过多），请求失败。 此外，Key Vault 会跟踪向限制值返回 429 计数的失败请求。 

如果出现限制值较高的有效业务用例，请与我们联系。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何针对服务限制来限制应用

以下是限制应用的最佳做法：
- 减少每个请求的操作数。
- 减少请求频率。
- 避免立即重试。 
    - 发出的所有请求要符合使用限制。

实现应用的错误处理时，请使用 HTTP 错误代码 429 检测是否需要限制客户端。 如果请求再次失败，错误代码为 HTTP 429，则仍会遇到 Azure 服务限制。 请继续使用推荐的客户端限制方法，重试请求直至成功。

### <a name="recommended-client-side-throttling-method"></a>推荐的客户端限制方法

出现 HTTP 错误代码 429 时，请使用指数延迟方法开始限制客户端：

1. 等待 1 秒，然后重试请求
2. 如果仍受限制，请等待 2 秒，然后重试请求
3. 如果仍受限制，请等待 4 秒，然后重试请求
4. 如果仍受限制，请等待 8 秒，然后重试请求
5. 如果仍受限制，请等待 16 秒，然后重试请求

此时，应不会收到 HTTP 429 响应代码。

## <a name="see-also"></a>另请参阅

若要深入了解 Microsoft 云中的限制，请参阅[限制模式](https://docs.microsoft.com/azure/architecture/patterns/throttling)。

