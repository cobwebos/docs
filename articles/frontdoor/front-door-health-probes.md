---
title: Azure 前门-后端运行状况监视 |Microsoft Docs
description: 本文可帮助你了解 Azure 前端如何监视后端的运行状况
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: c96dac55df2cdc15b7d3699e947c851a9fe69b02
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399627"
---
# <a name="health-probes"></a>运行状况探测

为了从给定的前门环境确定每个后端的运行状况和邻近性，每个前端环境会定期向每个已配置的后端发送综合 HTTP/HTTPS 请求。 Front Door 然后使用这些探测的响应来确定它应将实际客户端请求路由到的“最佳”后端。 

> [!WARNING]
> 由于前门具有全局边缘环境，因此后端的运行状况探测请求量可以非常高–每分钟25个请求，每分钟最高可达1200请求，具体取决于配置的运行状况探测频率。 如果默认探测频率为30秒，则后端的探测量应为每分钟约200请求。

## <a name="supported-protocols"></a>支持的协议

Front Door 支持通过 HTTP 或 HTTPS 协议发送探测。 这些探测通过为路由客户端请求配置的同一 TCP 端口发送，且不能重写。

## <a name="supported-http-methods-for-health-probes"></a>运行状况探测支持的 HTTP 方法

前门支持以下 HTTP 方法用于发送运行状况探测：

1. **GET：** GET 方法表示检索由请求 URI 标识的实体) 形式 (的任何信息。
2. **HEAD：** HEAD 方法与 GET 完全相同，不同之处在于服务器不能在响应中返回消息正文。 对于新的前门配置文件，默认情况下，探测方法设置为 HEAD。

> [!NOTE]
> 为了降低负载并降低后端成本，前门建议使用 HEAD 请求进行运行状况探测。

## <a name="health-probe-responses"></a>运行状况探测响应

| 响应  | 说明 | 
| ------------- | ------------- |
| 确定运行状况  |  200 OK 状态代码指示后端运行状况良好。 所有其他状态均视为失败。 如果出于任何原因（包括网络失败），探测未接收到有效的 HTTP 响应，则探测被视为失败。|
| 测量延迟  | 延迟是指从我们发送探测请求前的一刻到我们收到响应的最后一个字节的一刻所测得的时钟时间。 我们为每个请求都使用新的 TCP 连接，因此，此测量不会偏向使用现有热连接的后端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何确定后端运行状况

Azure 前门在所有算法中使用相同的三步骤过程来确定运行状况。

1. 排除已禁用的后端。

2. 排除具有运行状况探测错误的后端：
    * 此选择是通过查看最后 n__ 个运行状况探测响应来完成的。 如果至少有 x__ 个运行状况良好，则后端将被视为运行状况良好。

    * 通过更改负载平衡设置中的 SampleSize 属性来配置_n_ 。

    * 通过更改负载平衡设置中的 SuccessfulSamplesRequired 属性来配置_x_ 。

3. 从后端池的一组运行状况良好的后端中，Front Door 还将测量和维护每个后端的延迟（往返时间）。


## <a name="complete-health-probe-failure"></a>完成运行状况探测失败

如果后端池中每个后端的运行状况探测均为失败，则 Front Door 会将所有后端视为运行状况良好，并将跨所有后端在轮循机制分配中路由流量。

一旦任何后端返回到正常状态，则前门将恢复正常的负载平衡算法。

## <a name="disabling-health-probes"></a>禁用运行状况探测

如果后端池中有单个后端，则可以选择禁用运行状况探测，从而减少应用程序后端上的负载。 即使后端池中有多个后端，但其中只有一个已处于 "已启用" 状态，你也可以禁用运行状况探测。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
