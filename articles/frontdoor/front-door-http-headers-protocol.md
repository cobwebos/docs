---
title: Azure 前门中 HTTP 标头的协议支持 |微软文档
description: 本文介绍了前门支持的 HTTP 标头协议。
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471670"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure 前门中 HTTP 标头的协议支持
本文概述了前门支持与部分调用路径的协议（请参见图像）。 以下各节提供有关前门支持的 HTTP 标头的详细信息。

![Azure 前门 HTTP 标头协议][1]

>[!IMPORTANT]
>前门不认证此处未记录的任何 HTTP 标头。

## <a name="client-to-front-door"></a>客户端到 Front Door
前门接受来自传入请求的大多数标头，而无需修改它们。 如果发送，某些保留标头将从传入请求中删除，包括具有 X-FD-* 前缀的标头。

## <a name="front-door-to-backend"></a>Front Door 到后端

前门包括来自传入请求的标头，除非由于限制而被删除。 前门还添加了以下标头：

| 标头  | 示例和说明 |
| ------------- | ------------- |
| Via |  通过： 1.1 Azure </br> 前门添加客户端的 HTTP 版本，后跟*Azure*作为 Via 标头的值。 此标头指示客户端的 HTTP 版本，并且前门是客户端和后端之间请求的中间收件人。  |
| X-Azure-客户端IP | X-Azure-客户端IP： 127.0.0.1 </br> 表示与正在处理的请求关联的客户端 IP 地址。 例如，来自代理的请求可能会添加 X-前转 -For 标头以指示原始调用方的 IP 地址。 |
| X-Azure-套接字IP |  X-Azure-SocketIP： 127.0.0.1 </br> 表示与当前请求源自的 TCP 连接关联的套接字 IP 地址。 请求的客户端 IP 地址可能不等于其套接字 IP 地址，因为它可能被用户任意覆盖。|
| X-Azure-参考 |  X-Azure-参考： 0zxV_XAAAAKMkmmojBv2NT4TY6SQVjC0zv1NURHRTA2Mtkandm3Yzgyy2Qtmzyyys00YTU0Ltk0Yzmtnwzmza3NjQ3Nzzz </br> 标识前门提供的请求的唯一引用字符串。 它用于搜索访问日志，对故障排除至关重要。|
| X-Azure 请求链 |  X-Azure 请求链：跃点 =1 </br> 前门用于检测请求循环的标头，用户不应依赖它。 |
| X-Forwarded-For | X 转发- For： 127.0.0.1 </br> X 转发 -for （XFF） HTTP 标头字段通常标识通过 HTTP 代理或负载均衡器连接到 Web 服务器的客户端的原始 IP 地址。 如果存在现有的 XFF 标头，则前门会将客户端套接字 IP 追加到该标头中，或者使用客户端套接字 IP 添加 XFF 标头。 |
| X-Forwarded-Host | X 转发主机：contoso.azurefd.net </br> X-前行主机 HTTP 标头字段是一种常用方法，用于标识客户端在主机 HTTP 请求标头中请求的原始主机。 这是因为前端服务器处理请求的主机名可能不同。 |
| X-Forwarded-Proto | X 转发-原型： http </br> X-前行-Proto HTTP 标头字段通常用于标识 HTTP 请求的原始协议，因为基于配置的前门可能使用 HTTPS 与后端通信。 即使对反向代理的请求是 HTTP，也是如此。 |
| X-FD-健康探针 | X-FD-HealthProbe HTTP 标头字段用于从前门识别运行状况探测。 如果此标头设置为 1，则请求为运行状况探测。 当需要从特定的前门严格访问 X-前置主机头字段时，可以使用。 |

## <a name="front-door-to-client"></a>Front Door 到客户端

从后端发送到前门的任何标头也会传递到客户端。 以下是从前门发送到客户端的标头。

| 标头  | 示例 |
| ------------- | ------------- |
| X-Azure-参考 |  *X-Azure-参考： 0zxV_XAAAAKMkmmojBv2NT4TY6SQVjC0zv1NURHRTA2Mtkandm3Yzgyy2Qtmzyyys00YTU0Ltk0Yzmtnwzmza3NjQ3Nzzz* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 这对于故障排除至关重要，因为它用于搜索访问日志。|

## <a name="next-steps"></a>后续步骤

- [创建 Front Door](quickstart-create-front-door.md)
- [前门的工作原理](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
