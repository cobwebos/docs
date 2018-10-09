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
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038844"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door 服务 - HTTP 头协议支持
本文档简要介绍了 Azure Front Door 服务支持用于调用路径各部分的协议，如下图所示。 下面的部分详细介绍了 Front Door 所支持的 HTTP 头。

![Azure Front Door 服务 HTTP 头协议][1]

>[!WARNING]
>Azure Front Door 服务不对此处未记载的任何 HTTP 标头提供保证。

## <a name="1-client-to-front-door"></a>1.客户端到 Front Door
Front Door 接受来自传入请求的大多数标头（不修改它们），但是，如果发送了一些保留标头，则会将这些标头从传入请求中删除。 这包括具有以下前缀的标头：
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2.Front Door 到后端

Front Door 将包括来自传入请求的标头，除非这些标头由于上述限制而被删除。 Front Door 还将添加以下标头：

| 标头  | 示例和说明 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 它对于故障排除至关重要，因为它用于搜索访问日志。|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> 这是 Front Door 用于检测请求循环的标头，用户不应该依赖它。 |
| X-MS-Via |  *X-MS-Via: Azure* </br> 这是 Front Door 添加的标头，用于指示 Azure/Front Door 是客户端和后端之间请求的中间接收者。 |

## <a name="3-front-door-to-client"></a>3.Front Door 到客户端

下面是从 Front Door 发送到客户端的标头。 此外，从后端发送到 Front Door 的所有标头也传递给客户端。

| 标头  | 示例 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> 这是一个唯一的引用字符串，用于标识 Front Door 处理的请求。 它对于故障排除至关重要，因为它用于搜索访问日志。|

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png