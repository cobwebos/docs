---
title: Azure 前门中 HTTP 标头的协议支持 |Microsoft Docs
description: 本文介绍前门支持的 HTTP 标头协议。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: cd721f13ffa128e83072819a20b17f305118b13c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626286"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure 前门中 HTTP 标头的协议支持
本文概述了前门在调用路径的各个部分支持的协议 (参阅图像) 。 以下各节提供了有关前门支持的 HTTP 标头的详细信息。

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure 前门 HTTP 标头协议":::

>[!IMPORTANT]
>前门不验证此处未记录的任何 HTTP 标头。

## <a name="client-to-front-door"></a>客户端到 Front Door
前门接受传入请求的大多数标头而不进行修改。 如果发送了某些保留标头，则会将其从传入请求中删除，其中包含带有 X-FD-* 前缀的标头。

## <a name="front-door-to-backend"></a>Front Door 到后端

前门包含传入请求的标头，除非由于限制而被删除。 前门还添加以下标头：

| Header  | 示例和说明 |
| ------------- | ------------- |
| Via |  Via： 1.1 Azure </br> 前门添加客户端的 HTTP 版本，后跟 *Azure* 作为 Via 标头的值。 此标头指示客户端的 HTTP 版本，前门是客户端与后端之间的请求的中间接收方。  |
| X-Azure-ClientIP | X-ClientIP：127.0.0。1 </br> 表示与正在处理的请求关联的客户端 IP 地址。 例如，来自代理的请求可能会添加标头为 X 的标头，以指示原始调用方的 IP 地址。 |
| X-Azure-SocketIP |  X-SocketIP：127.0.0。1 </br> 表示与当前请求所源自的 TCP 连接关联的套接字 IP 地址。 请求的客户端 IP 地址可能不等于其套接字 IP 地址，因为用户可以随意覆盖它。|
| X-Azure-引用 |  X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> 标识由前门提供的请求的唯一引用字符串。 它用于搜索访问日志和故障排除的关键。|
| X-Azure-RequestChain |  X-RequestChain：跃点 = 1 </br> 前门用于检测请求循环的标头，用户不应依赖于该标头。 |
| X-Azure-FDID | X-FDID：55ce4ed1-4b06-4bf1-b40e-4638452104da<br/> 标识请求的引用字符串来自特定的前门资源。 可以在 Azure 门户中查看此值，也可以使用管理 API 来检索该值。 可以将此标头与 IP Acl 结合使用，将终结点锁定为仅接受来自特定前门资源的请求。 有关[更多详细信息](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)，请参阅常见问题 |
| X-Forwarded-For | X 转发的：127.0.0。1 </br> X 转发的 (XFF) HTTP 标头字段通常标识通过 HTTP 代理或负载均衡器连接到 web 服务器的客户端的原始 IP 地址。 如果存在现有的 XFF 标头，则前门会将客户端套接字 IP 追加到该标头，或使用客户端套接字 IP 添加 XFF 标头。 |
| X-Forwarded-Host | X 转发的主机： contoso.azurefd.net </br> "X 转发的主机 HTTP 标头" 字段是一种常用方法，用于标识宿主 HTTP 请求标头中的客户端请求的原始主机。 这是因为处理请求的后端服务器的前端的主机名可能不同。 |
| X-Forwarded-Proto | X-转发-Proto： http </br> X 转发的 HTTP 标头字段通常用于标识 HTTP 请求的原始协议，因为基于配置的前端可以使用 HTTPS 与后端通信。 即使向反向代理发出 HTTP 请求也是如此。 |
| X-FD-HealthProbe | HealthProbe HTTP 标头字段用于标识来自前门的运行状况探测。 如果此标头设置为1，则请求为运行状况探测。 如果要从特定前门使用 "X 转发主机标头" 字段来严格访问，可以使用。 |
|X-Azure-FDID | X-FDID 标头：437c82cd-360a-4a54-94c3-5ff707647783 </br> 此字段包含 frontdoorID，可用于标识传入请求来自哪个前门。 此字段由前门服务填充。 | 


## <a name="front-door-to-client"></a>Front Door 到客户端

从后端发送到前门的任何标头也会传递给客户端。 下面是从前门发送到客户端的标头。

| Header  | 示例 |
| ------------- | ------------- |
| X-Azure-引用 |  *X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是一个唯一的引用字符串，用于标识由前门提供的请求，这对于疑难解答至关重要，因为它用于搜索访问日志。|

## <a name="next-steps"></a>后续步骤

- [创建 Front Door](quickstart-create-front-door.md)
- [前门的工作方式](front-door-routing-architecture.md)
