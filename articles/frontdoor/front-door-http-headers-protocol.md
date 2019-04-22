---
title: 协议支持的 Azure 第一道防线服务中的 HTTP 标头 |Microsoft Docs
description: 本文介绍第一道防线服务支持的 HTTP 标头协议。
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861836"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>在 Azure 第一道防线服务中的 HTTP 标头的协议支持
本文概述了与的调用路径部分的第一道防线服务支持的协议 （请参阅图）。 以下部分提供有关支持的第一道防线服务的 HTTP 标头的详细信息。

![Azure Front Door 服务 HTTP 头协议][1]

>[!IMPORTANT]
>第一道防线服务不会认证不此处记录任何 HTTP 标头。

## <a name="client-to-front-door-service"></a>第一道防线服务客户端
第一道防线服务接受传入请求中的大部分标头，而无需修改它们。 从传入请求如果发送的包括带有 X 的标头中删除某些保留的标头-FD-* 前缀。

## <a name="front-door-service-to-backend"></a>第一道防线到后端服务

第一道防线服务包括从传入请求的标头，除非由于限制中删除。 第一道防线还添加了以下标头：

| 标头  | 示例和说明 |
| ------------- | ------------- |
| Via |  通过：1.1 Azure </br> 第一道防线添加客户端的 HTTP 版本后跟*Azure*作为 Via 标头的值。 这表示客户端的 HTTP 版本和该第一道防线是请求客户端和后端之间的中间收件人。  |
| X-Azure-ClientIP | X-Azure-ClientIP:127.0.0.1 </br> 表示与处理的请求关联的客户端 IP 地址。 例如，来自代理的请求可能会添加 X-转发-对于标头以指示原始调用方的 IP 地址。 |
| X-Azure-SocketIP |  X-Azure-SocketIP:127.0.0.1 </br> 表示与当前请求所源自的 TCP 连接关联的套接字 IP 地址。 请求的客户端 IP 地址不可能等于其套接字的 IP 地址，因为用户可以随意覆盖。|
| X-Azure-Ref |  X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> 标识由第一道防线提供服务的请求的唯一引用字符串。 它用于搜索访问日志和故障排除的关键。|
| X-Azure-RequestChain |  X-Azure-RequestChain： 跃点 = 1 </br> 第一道防线使用来检测请求循环和用户应不依赖于它标头。 |
| X-Forwarded-For | X-转发-对于：127.0.0.1 </br> X-Forwarded-For (XFF) HTTP 标头字段通常标识连接到 web 服务器通过 HTTP 代理服务器或负载均衡器的客户端的原始 IP 地址。 如果没有现有的 XFF 标头，第一道防线将客户端套接字 IP 追加到其中，或添加具有客户端套接字 IP 的 XFF 标头。 |
| X-Forwarded-Host | X 转发主机： contoso.azurefd.net </br> X 转发主机 HTTP 标头字段是用来标识原始主机由主机 HTTP 请求标头中的客户端请求的常见方法。 这是因为来自第一道防线的主机名可能不同的后端服务器处理请求。 |
| X-Forwarded-Proto | X 转发 Proto: http </br> X 转发 Proto HTTP 标头字段通常用于标识的 HTTP 请求的原始协议，因为第一道防线，根据配置，可能会与后端使用 HTTPS 进行通信。 即使对反向代理的请求为 HTTP，这是如此。 |

## <a name="front-door-service-to-client"></a>第一道防线服务向客户端

从后端发送到第一道防线任何标头还将传递到客户端。 以下是从第一道防线发送到客户端的标头。

| 标头  | 示例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 这是故障排除，因为它用于访问日志中搜索的关键。|

## <a name="next-steps"></a>后续步骤

- [创建第一道防线](quickstart-create-front-door.md)
- [第一道防线的工作原理](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png