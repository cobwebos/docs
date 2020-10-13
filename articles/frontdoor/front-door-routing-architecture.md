---
title: Azure 前端-路由体系结构 |Microsoft Docs
description: 本文以宏观视角对 Front Door 体系结构进行整体综述和梳理。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449185"
---
# <a name="routing-architecture-overview"></a>路由体系结构概述

当 Azure 前门接收客户端请求时，它将执行以下两项操作之一。 如果启用缓存，或将其转发到相应的应用程序后端作为反向代理，则可以回答这些问题。

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>选择 Front Door 环境来实现流量路由（任意广播）

路由到 Azure 前门环境的流量对 DNS (域名系统) 和 HTTP (超文本传输协议) 流量使用 [任意播](https://en.wikipedia.org/wiki/Anycast) ，这允许用户请求在最少的网络跃点中到达最接近的环境。 此体系结构通过最大程度地提高拆分 TCP 的优点，为最终用户提供更好的往返时间。 Front Door 将其环境组织为主“环”和回退“环”。 外环提供更接近用户的环境，延迟较低。  内部环具有可处理外部环形环境故障转移的环境，以防出现任何问题。 外环是所有流量的首选目标，内部环是处理来自外部环的流量溢出。 由前门提供的每个前端主机或域都为虚拟 Internet 协议地址 (虚拟 Internet 协议地址) ，该地址由内部和外部环中的环境公布。 回退 VIP 仅由内环中的环境公布。 

此体系结构可确保来自最终用户的请求始终到达最近的前门环境。 即使首选前门环境不正常，所有流量也会自动转移到下一个最近的环境。

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>连接到 Front Door 环境（拆分 TCP）

[拆分 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) 是一种用于减少延迟和 TCP 问题的技术，其原理是将会产生较长往返时间的连接拆分成较小片段。 当前门环境离最终用户更近时，TCP 连接将在前门环境内终止。 在应用程序后端)  (RTT 较长的 TCP 连接拆分为两个单独的连接。 最终用户与前门环境之间的 "短连接" 意味着连接是通过三次短行程（而不是三次往返行程）建立的，这会导致节省滞后时间。 前门环境和后端之间的 "长连接" 可以是预先建立的，然后跨其他最终用户重新使用请求可节省连接时间。 在建立 SSL/TLS (传输层安全性) 连接时，拆分 TCP 的影响将会成倍增加，因为需要更多的往返行程来保护连接。

## <a name="processing-request-to-match-a-routing-rule"></a>处理请求时将其与路由规则进行匹配
建立连接并完成 TLS 握手后，请求落在前门环境之后的第一步是将其与路由规则进行匹配。 匹配由前门上的配置确定，特定的路由规则与请求匹配。 阅读和详细了解 Front Door 如何执行[路由匹配](front-door-route-matching.md)。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>确定后端池中可用于路由规则的后端
当前门满足传入请求的路由规则后，下一步是获取与路由规则关联的后端池的运行状况探测状态（如果没有缓存）。 阅读和详细了解 Front Door 如何使用[运行状况探测](front-door-health-probes.md)监视后端运行状况。

## <a name="forwarding-the-request-to-your-application-backend"></a>将请求转发到应用程序后端
最后，假设未配置缓存，用户请求将根据 [路由方法](front-door-routing-methods.md) 配置转发到 "最佳" 后端。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
