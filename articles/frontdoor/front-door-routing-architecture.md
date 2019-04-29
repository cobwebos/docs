---
title: Azure Front Door 服务 - 路由体系结构 | Microsoft Docs
description: 本文以宏观视角对 Front Door 体系结构进行整体综述和梳理。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736270"
---
# <a name="routing-architecture-overview"></a>路由体系结构概述

Azure Front Door 服务收到客户端请求后，会应答请求（如果启用了缓存）或将其转发到相应的应用程序后端（作为反向代理）。

</br>路由到 Azure Front Door 或路由到后端有可能使通信流得到优化。

## <a name = "anycast"></a>选择 Front Door 环境来实现流量路由（任意广播）

路由到 Azure Front Door 环境这种方式将[任意广播](https://en.wikipedia.org/wiki/Anycast)同时用于 DNS（域名系统）和 HTTP（超文本传输协议）流量，这样用户流量会依照网络拓扑转到最近的环境（跃点最少）。 此体系结构通常可为最终用户优化所需的往返时间（最大程度发挥拆分 TCP 的优势）。 Front Door 将其环境组织为主“环”和回退“环”。  外环提供更接近用户的环境，延迟较低。  内环中的环境可在外环环境发生问题时为其执行故障转移。 外环是所有流量的首选目标，但需要使用内环来处理来自外环的流量溢出。 就 VIP（虚拟 Internet 协议地址）而言，会向每个前端主机或由 Front Door 提供的域分配一个主 VIP（由内环和外环中的环境发布）和一个回退 VIP（仅由内环中的环境发布）。 

</br>此整体策略可确保来自最终用户的请求始终能够到达最近的 Front Door 环境，且即使首选的 Front Door 环境处于不良状态，流量也能够自动移动到下一个最近的环境。

## <a name = "splittcp"></a>连接到 Front Door 环境（拆分 TCP）

[拆分 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) 是一种用于减少延迟和 TCP 问题的技术，其原理是将会产生较长往返时间的连接拆分成较小片段。  通过将 Front Door 环境设置在较接近最终用户的位置并在 Front Door 环境内部终止 TCP 连接，可将一个产生较长往返时间 (RTT) 的 TCP 连接（到应用程序后端）拆分为两个 TCP 连接。 最终用户和 Front Door 环境之间的短连接意味着通过三个短往返行程而非三个长往返行程建立了连接，由此降低了延迟。  Front Door 环境和后端之间的长连接可预先建立并可跨多个最终用户调用重复使用，同样节省了 TCP 连接时间。  如果建立的是 SSL/TLS（传输层安全性）连接，效果将倍增，因为会通过更多往返行程来确保连接的安全性。

## <a name="processing-request-to-match-a-routing-rule"></a>处理请求时将其与路由规则进行匹配
建立连接并执行 SSL 握手之后，当请求进入 Front Door 环境时，首先要做的是匹配路由规则。 此匹配基本上是指根据 Front Door 中的所有配置确定将请求与哪个特定路由规则进行匹配。 阅读和详细了解 Front Door 如何执行[路由匹配](front-door-route-matching.md)。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>确定后端池中可用于路由规则的后端
Front Door 基于传入的请求找到路由规则的匹配项后，如果没有缓存，则下一步是提取与匹配的路由关联的后端池的运行状况探测状态。 阅读和详细了解 Front Door 如何使用[运行状况探测](front-door-health-probes.md)监视后端运行状况。

## <a name="forwarding-the-request-to-your-application-backend"></a>将请求转发到应用程序后端
最后，假设没有配置缓存，系统会基于 [Front Door 路由方法](front-door-routing-methods.md)配置将用户请求转发到“最佳”后端。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
