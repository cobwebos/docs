---
title: Azure 中的负载均衡器 TCP 重置和空闲超时
titleSuffix: Azure Load Balancer
description: 本文介绍具有空闲超时情况下发送双向 TCP RST 数据包功能的负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 060048bf786f424d5df6eb8fb4813877acb0fea0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823205"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>负载均衡器 TCP 重置和空闲超时

可以使用[标准负载均衡器](load-balancer-standard-overview.md)，通过为给定规则启用“空闲时执行 TCP 重置”，为方案创建可预测度更高的应用程序行为。 负载均衡器的默认行为是当达到流的空闲超时的情况下，以静默方式删除流。  启用此功能将导致负载均衡器在空闲超时情况下发送双向 TCP 重置（TCP RST 包）。  这将通知应用程序终结点，连接已超时且不再可用。  终结点可以视需要立即建立新连接。

![负载均衡器 TCP 重置](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP 重置

你可以更改此默认行为，并启用根据入站 NAT 规则、负载均衡规则和[出站规则](https://aka.ms/lboutboundrules)在空闲超时情况下发送 TCP 重置。  根据规则启用时，负载均衡器将在所有匹配流的空闲超时情况下向客户端和服务器终结点发送双向 TCP 重置（TCP RST 数据包）。

接收 TCP RST 数据包的终结点会立即关闭相应的套接字。 这会立即通知终结点该连接已释放，并且同一 TCP 连接上的任何后续通信都将失败。  应用程序可以在套接字关闭时清除连接，并根据需要重新建立连接，而无需等待 TCP 连接最终超时。

对于许多方案，这样可以减少发送 TCP（或应用层）保持连接以刷新流空闲超时的需要。 

如果空闲持续时间超过配置所允许的持续时间，或者你的应用程序显示启用了 TCP 重置的不良行为，则可能仍需要使用 TCP 保持连接（或应用层保持连接）来监视 TCP 连接的活跃性。  此外，当路径中某处的连接已经过代理时，保持连接（特别是应用层保持连接）也仍然有用。  

请仔细检查整个端到端方案，确定能否从启用 TCP 重置、调整空闲超时中受益，以及是否需要执行其他步骤来确保所需的应用程序行为。

## <a name="configurable-tcp-idle-timeout"></a>可配置的 TCP 空闲超时

Azure 负载均衡器的空闲超时范围如下：
-  对于出站规则为4分钟到100分钟
-  负载均衡器规则和入站 NAT 规则4到30分钟

默认情况下，它设置为 4 分钟。 如果处于非活动状态的时间超过超时值，则不能保证在客户端和云服务之间保持 TCP 或 HTTP 会话。

当连接关闭时，客户端应用程序可能会收到以下错误消息：“The underlying connection was closed:A connection that was expected to be kept alive was closed by the server.”（基础连接已关闭: 服务器关闭了应保持连接状态的连接。）

常见的做法是使用 TCP 保持连接状态。 这种做法可以使连接状态保持更长时间。 有关详细信息，请参阅 [.NET 示例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 在启用保持连接状态的情况下，在连接处于非活动状态时发送数据包。 用于保持连接状态的数据包将确保不会达到空闲超时值，并且连接会维持很长时间。

此设置仅适用于入站连接。 为了避免断开连接，请将 TCP 保持连接的时间间隔配置为小于空闲超时设置，或者提高空闲超时值。 为了支持这些方案，我们添加了可配置空闲超时支持。

TCP 保持连接状态适用于电池续航时间不受限制的情况。 不建议将其用于移动应用程序。 在移动应用程序中使用 TCP 保持连接状态可能会加快设备电池的耗尽速度。


## <a name="limitations"></a>限制

- TCP 重置仅在已建立状态的 TCP 连接期间发送。
- 不会为配置了 HA 端口的内部负载均衡器发送 TCP 重置。
- TCP 空闲超时不影响 UDP 协议的负载均衡规则。

## <a name="next-steps"></a>后续步骤

- 了解 [标准负载均衡器](load-balancer-standard-overview.md)。
- 了解 [出站规则](load-balancer-outbound-rules-overview.md)。
- [在空闲超时配置 TCP RST](load-balancer-tcp-idle-timeout.md)
