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
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951128"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door 服务 - HTTP 头协议支持
本文档简要介绍了 Azure Front Door 服务支持用于调用路径各部分的协议，如下图所示。 下面的部分详细介绍了 Front Door 所支持的 HTTP 头。

![Azure Front Door 服务 HTTP 头协议][1]

>[!WARNING]
>Azure Front Door 服务不对此处未记载的任何 HTTP 标头提供保证。

## <a name="1-client-to-front-door"></a>1.客户端到 Front Door

Front Door 将解析以下标头。 对于任何其他标头，Front Door 将充当传递通路并将标头发送到应用程序后端。

| 标头  | 示例和说明 |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> 一个 GUID，它用于标识为呈现单个网页而生成的一个或多个 HTTP 请求。 Front Door 将使用 X-FD-ImpressionGuid 标头将 GUID 转发到后端。 </br> 如果客户端未发送此标头，Front Door 将自动生成新的 GUID 并将其发送到后端。 |

## <a name="2-front-door-to-backend"></a>2.Front Door 到后端

以下标头将从 Front Door 发送到应用程序后端。

| 标头  | 示例和说明 |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> 此 ID 用于标识 Front Door 提供的请求。 此标头值是用于故障排除的最关键的信息。 此 ID 可用于搜索访问日志。 Front Door 将向客户端和后端发送同一标头。 |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>客户端与 Front Door之间的 HTTP 协议版本。 可能的值包括：1.1、2.0 等。 |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>“客户端” Internet 协议地址，它与正在处理的请求相关联 |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>处理请求的特定 Front Door 节点。 后端可记录此标头，用于帮助调试。 但是，不得依赖于 Front Door 命名约定、Front Door 节点之间的流量分配或特定单个节点的可用性。 |
| X FD EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>一个唯一标识符 (GUID)，它是在请求处理开始时创建的。 处理此请求时创建的所有访问日志都将与同一 GUID 进行关联。 |
| X FD ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>一个 GUID，它用于标识为呈现单个网页而生成的一个或多个 HTTP 请求。 客户端可通过以下方式指定此 GUID： </br>- 在 X-MSEdge-IG 请求头中发送。 例如：X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>- 在“ig”查询字符串中发送。 例如：?ig=2BC2B67F49ED47DFA47FBE2E962AC81F。 如果客户端未发送 GUID，则 Front Door 将生成新的 GUID。 |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> 客户端发出的原始请求的 URL。 |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>一个标识符，它属于正在为其处理请求的 Front Door 配置文件。 借助它，为不同的 Front Door 托管多个应用程序的后端可确定其要对哪个 Front Door 配置处理当前请求。 |
| X-FD-RequestHadClientId | 保留 |
| X-FD-ResponseHasClientId | 保留 |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>与上述 X-FD-ClientIP 相同 |

## <a name="3-front-door-to-client"></a>3.Front Door 到客户端

下面是从 Front Door 发送到客户端的标头。 此外，从后端发送到 Front Door 的所有标头也传递给客户端。

| 标头  | 示例 |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>此 ID 用于标识 Front Door 提供的请求。 此标头值是用于故障排除的最关键的信息。 此 ID 可用于搜索访问日志。 Front Door 将向客户端和后端发送同一标头。 |

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png