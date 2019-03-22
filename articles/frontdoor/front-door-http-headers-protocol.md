---
title: Azure Front Door 服务 - HTTP 头协议支持 | Microsoft Docs
description: 本文将帮助你了解 Front Door 所支持的 HTTP 头协议
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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726322"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door 服务 - HTTP 头协议支持
本文档简要介绍了 Azure Front Door 服务支持用于调用路径各部分的协议，如下图所示。 下面的部分详细介绍了 Front Door 所支持的 HTTP 头。

![Azure Front Door 服务 HTTP 头协议][1]

>[!WARNING]
>Azure Front Door 服务不对此处未记载的任何 HTTP 标头提供保证。

## <a name="1-client-to-front-door"></a>1.客户端到 Front Door
Front Door 接受来自传入请求的大多数标头（不修改它们），但是，如果发送了一些保留标头，则会将这些标头从传入请求中删除。 这包括具有以下前缀的标头：
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2.Front Door 到后端

Front Door 将包括来自传入请求的标头，除非这些标头由于上述限制而被删除。 Front Door 还将添加以下标头：

| 标头  | 示例和说明 |
| ------------- | ------------- |
| Via |  *通过：1.1 Azure* </br> 第一道防线添加 Azure 的值为跟通过标头的客户端的 HTTP 版本。 这添加以指示客户端的 HTTP 版本和该 Azure 的第一道防线是请求客户端和后端之间的中间收件人。  |
| X-Azure-ClientIP | *X-Azure-ClientIP:127.0.0.1* </br> 表示与处理的请求关联的"客户端"Internet 协议地址。 例如，来自代理的请求可能会添加 X-转发-对于标头以指示原始调用方的 IP 地址。 |
| X-Azure-SocketIP |  *X-Azure-SocketIP:127.0.0.1* </br> 表示与 TCP 连接，源自当前请求关联的套接字 Internet 协议地址。 请求的客户端 IP 地址不可能等于其套接字的 IP 地址，因为被最终用户可以随意覆盖。|
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 它对于故障排除至关重要，因为它用于搜索访问日志。|
| X-Azure-RequestChain |  *X-Azure-RequestChain： 跃点 = 1* </br> 这是 Front Door 用于检测请求循环的标头，用户不应该依赖它。 |
| X-Forwarded-For | *X-转发-对于：127.0.0.1* </br> X-Forwarded-For (XFF) HTTP 标头字段是用于标识连接到 web 服务器通过 HTTP 代理服务器或负载均衡器的客户端的原始 IP 地址的常见方法。 如果没有现有 XFF 标头，则第一道防线追加到其他客户端套接字 IP 添加具有客户端套接字 IP 的 XFF 标头。 |
| X-Forwarded-Host | *X 转发主机： contoso.azurefd.net* </br> X 转发主机 HTTP 标头字段是用于标识原始请求的主机 HTTP 请求标头中的客户端，因为来自第一道防线的主机名可能不同的处理请求的后端服务器的主机的常见方法。 |
| X-Forwarded-Proto | *X 转发 Proto: http* </br> X 转发 Proto HTTP 标头字段是用于标识 HTTP 请求的原始协议，因为根据配置，第一道防线可能与后端使用 HTTPS，即使对反向代理的请求是 HTTP 进行通信的常见方法。 |

## <a name="3-front-door-to-client"></a>3.Front Door 到客户端

下面是从 Front Door 发送到客户端的标头。 此外，从后端发送到 Front Door 的所有标头也传递给客户端。

| 标头  | 示例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 它对于故障排除至关重要，因为它用于搜索访问日志。|

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png