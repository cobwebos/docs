---
title: Azure 前门 - 后端运行状况监控 |微软文档
description: 本文可帮助您了解 Azure 前门如何监视后端的运行状况
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471568"
---
# <a name="health-probes"></a>运行状况探测

为了确定每个后端从给定的前门环境的运行状况和邻近性，每个前门环境都会定期向每个配置的后端发送综合 HTTP/HTTPS 请求。 Front Door 然后使用这些探测的响应来确定它应将实际客户端请求路由到的“最佳”后端。 

> [!WARNING]
> 由于前门在全球有许多边缘环境，因此运行状况探测器向后端请求的音量可能相当高 - 根据配置的运行状况探测频率，每分钟从 25 个请求到每分钟高达 1200 个请求。 默认探测频率为 30 秒，后端上的探头体积应约为每分钟 200 个请求。

## <a name="supported-protocols"></a>支持的协议

Front Door 支持通过 HTTP 或 HTTPS 协议发送探测。 这些探测通过为路由客户端请求配置的同一 TCP 端口发送，且不能重写。

## <a name="supported-http-methods-for-health-probes"></a>支持运行状况探测器的 HTTP 方法

前门支持以下用于发送运行状况探测器的 HTTP 方法：

1. **获取：** GET 方法意味着检索请求 URI 标识的任何信息（以实体的形式）。
2. **头：** HEAD 方法与 GET 相同，只是服务器不得在响应中返回消息正文。 默认情况下，对于新的前门配置文件，探头方法设置为 HEAD。

> [!NOTE]
> 为了降低后端的负载和成本，前门建议使用 HEAD 请求进行运行状况探头。

## <a name="health-probe-responses"></a>运行状况探测响应

| 响应  | 描述 | 
| ------------- | ------------- |
| 确定运行状况  |  200 OK 状态代码指示后端运行状况良好。 所有其他状态均视为失败。 如果出于任何原因（包括网络失败），探测未接收到有效的 HTTP 响应，则探测被视为失败。|
| 测量延迟  | 延迟是指从我们发送探测请求前的一刻到我们收到响应的最后一个字节的一刻所测得的时钟时间。 我们为每个请求都使用新的 TCP 连接，因此，此测量不会偏向使用现有热连接的后端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何确定后端运行状况

Azure 前门在所有算法中使用相同的三步过程来确定运行状况。

1. 排除已禁用的后端。

2. 排除具有运行状况探测错误的后端：
    * 此选择是通过查看最后 n__ 个运行状况探测响应来完成的。 如果至少有 x__ 个运行状况良好，则后端将被视为运行状况良好。

    * _n_通过在负载平衡设置中更改 SampleSize 属性进行配置。

    * _x_是通过在负载平衡设置中更改成功采样必需属性来配置的。

3. 从后端池的一组运行状况良好的后端中，Front Door 还将测量和维护每个后端的延迟（往返时间）。


## <a name="complete-health-probe-failure"></a>完成运行状况探测失败

如果后端池中每个后端的运行状况探测均为失败，则 Front Door 会将所有后端视为运行状况良好，并将跨所有后端在轮循机制分配中路由流量。

一旦任何后端恢复到正常状态，前门将恢复正常的负载平衡算法。

## <a name="disabling-health-probes"></a>禁用运行状况探测器

如果后端池中只有一个后端，则可以选择禁用运行状况探测器，以减少应用程序后端的负载。 即使您在后端池中有多个后端，但只有一个后端处于启用状态，您也可以禁用运行状况探测。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
