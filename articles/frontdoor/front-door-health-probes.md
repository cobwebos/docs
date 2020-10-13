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
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449238"
---
# <a name="health-probes"></a>运行状况探测

为了确定给定前门环境的每个后端的运行状况和邻近性，每个前端环境会定期向每个已配置的后端发送综合 HTTP/HTTPS 请求。 然后，前门使用探测器中的这些响应来确定用于路由客户端请求的 "最佳" 后端资源。 

> [!WARNING]
> 由于前端具有全局边缘环境，因此后端的运行状况探测量可以非常高-从25个请求每分钟到最高1200请求，具体取决于配置的运行状况探测频率。 如果默认探测频率为30秒，则后端的探测量应为每分钟约200请求。

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
| 确定运行状况  |  200 OK 状态代码指示后端运行状况良好。 所有其他状态均视为失败。 如果出于任何原因 (包括网络故障) 没有收到探测的有效 HTTP 响应，则会将探测计为失败。|
| 测量延迟  | 延迟是指从我们发送探测请求前的一刻到我们收到响应的最后一个字节的一刻所测得的时钟时间。 我们为每个请求使用一个新的 TCP 连接，因此，此度量值并不偏离现有的热连接后端。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door 如何确定后端运行状况

Azure 前门在所有算法中使用相同的三步骤过程来确定运行状况。

1. 排除已禁用的后端。

2. 排除具有运行状况探测错误的后端：
    * 此选择是通过查看最后 n__ 个运行状况探测响应来完成的。 如果至少有 x__ 个运行状况良好，则后端将被视为运行状况良好。

    * 通过更改负载平衡设置中的 SampleSize 属性来配置_n_ 。

    * 通过更改负载平衡设置中的 SuccessfulSamplesRequired 属性来配置_x_ 。

3. 对于后端池中的正常后端集，前门还会进一步测量并维持每个后端) 的 (延迟时间。


## <a name="complete-health-probe-failure"></a>完成运行状况探测失败

如果后端池中每个后端的运行状况探测均为失败，则 Front Door 会将所有后端视为运行状况良好，并将跨所有后端在轮循机制分配中路由流量。

一旦任何后端返回到正常状态，则前门将恢复正常的负载平衡算法。

## <a name="disabling-health-probes"></a>禁用运行状况探测

如果后端池中有单个后端，则可以选择禁用运行状况探测，从而减少应用程序后端上的负载。 即使后端池中有多个后端，但其中只有一个已处于 "已启用" 状态，你也可以禁用运行状况探测。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
