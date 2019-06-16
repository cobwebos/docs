---
title: Azure Front Door 服务 - 后端运行状况监视 | Microsoft 文档
description: 本文将帮助你了解 Azure Front Door 服务如何监视后端运行状况
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
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736575"
---
# <a name="health-probes"></a>运行状况探测

为了确定每个后端的运行状况，每个 Front Door 环境将定期向每个配置的后端发送综合 HTTP/HTTPS 请求。 Front Door 然后使用这些探测的响应来确定它应将实际客户端请求路由到的“最佳”后端。


## <a name="supported-protocols"></a>支持的协议

Front Door 支持通过 HTTP 或 HTTPS 协议发送探测。 这些探测通过为路由客户端请求配置的同一 TCP 端口发送，且不能重写。

## <a name="health-probe-responses"></a>运行状况探测响应

| Responses  | 描述 | 
| ------------- | ------------- |
| 确定运行状况  |  200 OK 状态代码指示后端运行状况良好。 所有其他状态均视为失败。 如果出于任何原因（包括网络失败），探测未接收到有效的 HTTP 响应，则探测被视为失败。|
| 测量延迟  | 延迟是指从我们发送探测请求前的一刻到我们收到响应的最后一个字节的一刻所测得的时钟时间。 我们为每个请求都使用新的 TCP 连接，因此，此测量不会偏向使用现有热连接的后端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何确定后端运行状况

Azure Front Door 服务在所有算法中均使用下面相同的三步过程来确定运行状况。

1. 排除已禁用的后端。

2. 排除具有运行状况探测错误的后端：
    * 此选择是通过查看最后 n  个运行状况探测响应来完成的。 如果至少有 x  个运行状况良好，则后端将被视为运行状况良好。

    * 通过更改负载平衡设置中的 SampleSize 属性配置 n  值。

    * 通过更改负载平衡设置中的 SuccessfulSamplesRequired 属性配置 x  值。

3. 从后端池的一组运行状况良好的后端中，Front Door 还将测量和维护每个后端的延迟（往返时间）。


## <a name="complete-health-probe-failure"></a>完成运行状况探测失败

如果后端池中每个后端的运行状况探测均为失败，则 Front Door 会将所有后端视为运行状况良好，并将跨所有后端在轮循机制分配中路由流量。

一旦有后端返回到正常运行状态，Front Door 即恢复正常负载均衡算法。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
