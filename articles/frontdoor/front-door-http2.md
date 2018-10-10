---
title: Azure Front Door 服务 - HTTP2 支持 | Microsoft Docs
description: 本文有助于了解 Azure Front Door 服务中的 HTTP/2 支持
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 4282c9e9b660476992ba6f948bc5e408e9b064a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968605"
---
# <a name="http2-support-in-azure-front-door-service"></a>Azure Front Door 服务中的 HTTP/2 支持
HTTP/2 是 HTTP/1.1 的主要修订。 它提供更快的 Web 性能、减少了响应时间并改进了用户体验，同时保留了熟悉的 HTTP 方法、状态代码和语义。 尽管 HTTP/2 用于处理 HTTP 和 HTTPS，但许多客户端 Web 浏览器仅通过传输层安全性 (TLS) 支持 HTTP/2。

### <a name="http2-benefits"></a>HTTP/2 优点

HTTP/2 的优点包括：

*   **多路复用和并发**

    使用 HTTP 1.1，发出多个资源请求需要多个 TCP 连接，每个连接都有与之关联的性能开销。 HTTP/2 允许通过单个 TCP 连接请求多个资源。

*   **标头压缩**

    通过压缩得到服务资源的 HTTP 标头，显著减少网络上的时间。

*   **流依赖关系**

    客户端使用流依赖关系可向服务器表明哪些资源具有优先级。


## <a name="http2-browser-support"></a>HTTP/2 浏览器支持

所有主流浏览器均已在其当前版本中实现 HTTP/2 支持。 不支持的浏览器会自动回退到 HTTP/1.1。

|浏览器|最低版本|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>启用 Azure Front Door 服务中的 HTTP/2 支持

目前，HTTP/2 支持对于所有 Front Door 配置均有效。 客户无需进一步执行操作。

## <a name="next-steps"></a>后续步骤

若要了解有关 HTTP/2 的详细信息，请访问以下资源：

- [HTTP/2 规范主页](https://http2.github.io/)
- [正式版 HTTP/2 常见问题解答](https://http2.github.io/faq/)
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
