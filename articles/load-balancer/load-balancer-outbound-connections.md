---
title: 出站代理 Azure 负载均衡器
description: 介绍如何使用 Azure 负载均衡器作为出站 internet 连接的代理
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017686"
---
# <a name="outbound-proxy-azure-load-balancer"></a>出站代理 Azure 负载均衡器

Azure 负载均衡器可用作出站 internet 连接的代理。 负载均衡器为后端实例提供出站连接。 

此配置 ** (SNAT) 使用源网络地址转换 **。 SNAT 将后端的 IP 地址重写为负载均衡器的公共 IP 地址。 

SNAT 允许对后端实例进行 **IP 伪装** 。 此伪装阻止外部源将直接地址用于后端实例。 

在后端实例之间共享 IP 地址可降低静态公共 Ip 的成本并支持各种方案，例如，使用来自已知公共 Ip 的流量简化 IP 允许列表。 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> 跨资源共享端口

如果负载均衡器的后端资源没有实例级公共 IP (ILPIP) 地址，则它们通过公共负载均衡器的前端 IP 建立出站连接。

端口用于生成用于维护不同流的唯一标识符。 Internet 使用5元组来提供这种区别。

5元组包含：

* 目标 IP
* 目标端口
* 源 IP
* 提供此区别的源端口和协议。

如果端口用于入站连接，则它将具有该端口上的入站连接请求的 **侦听器** ，且不能用于出站连接。 

若要建立出站连接，必须使用 **临时端口** 向目标提供一个端口，以便在该端口上进行通信并维护不同的流量流。 

每个 IP 地址具有65535个端口。 前1024个端口保留为 **系统端口**。 每个端口都可用于 TCP 和 UDP 的入站或出站连接。 

对于其余端口，Azure 提供64000作为 **临时端口**使用。 将 IP 地址添加为前端 IP 配置时，可将这些临时端口用于 SNAT。

通过出站规则，可以将这些 SNAT 端口分发给后端实例，使其能够共享负载均衡器的公共 IP () 用于出站连接。

主机上每个后端实例的网络将对作为出站连接一部分的数据包执行 SNAT。 主机将源 IP 重写到一个公共 Ip。 主机将每个出站数据包的源端口重写到某个 SNAT 端口。

## <a name="exhausting-ports"></a><a name="scenarios"></a> 耗尽端口

与同一目标 IP 和目标端口的每个连接都将使用 SNAT 端口。 此连接维护从后端实例或**客户端**到**服务器**的不同**流量流**。 此过程为服务器提供了一个用于对流量进行寻址的不同端口。 如果没有此进程，客户端计算机将不知道数据包所属的流。

假设有多个浏览器转到 https://www.microsoft.com ，这是：

* 目标 IP = 23.53.254.142
* 目标端口 = 443
* 协议 = TCP

如果没有不同的目标端口来返回流量 (用于建立连接) 的 SNAT 端口，则客户端将无法从一个查询结果中分离另一个查询结果。

出站连接可能会突发。 后端实例可以分配的端口不足。 如果不启用 **连接重用** ，则会增加 SNAT **端口耗尽** 的风险。

发生端口耗尽时，与目标 IP 建立的新出站连接将会失败。 当端口可用时，连接将成功。 当来自 IP 地址的64000端口分布在多个后端实例中时，就会发生此耗尽。 有关如何缓解 SNAT 端口耗尽的指导，请参阅 [故障排除指南](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)。  

对于 TCP 连接，负载均衡器将为每个目标 IP 和端口使用单个 SNAT 端口。 此多次允许多个连接到具有相同 SNAT 端口的同一目标 IP。 如果连接不同于不同的目标端口，此多次会受到限制。

对于 UDP 连接，负载均衡器使用 **端口限制的圆锥 NAT** 算法，该算法每个目标 IP 使用一个 SNAT 端口，不管目标端口如何。 

端口可重复用于无限数量的连接。 仅当目标 IP 或端口不同时，才重新使用端口。

## <a name="port-allocation"></a><a name="preallocatedports"></a> 端口分配

作为负载均衡器的前端 IP 分配的每个公共 IP 都为其后端池成员提供64000个 SNAT 端口。 无法与后端池成员共享端口。 一系列 SNAT 端口只能由单个后端实例使用，以确保正确路由返回数据包。 

建议使用显式出站规则来配置 SNAT 端口分配。 此规则将最大限度地提高每个后端实例可用于出站连接的 SNAT 端口数。 

如果通过负载均衡规则使用出站 SNAT 自动分配，则分配表将定义你的端口分配。

下 <a name="snatporttable"></a> 表显示后端池大小层的 SNAT 端口针对：

| 池大小（VM 实例） | 每个 IP 配置的预先分配 SNAT 端口 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> 如果你有一个后端池，且最大大小为6，则在定义显式出站规则的情况下，每个实例都可以具有 64000/10 = 6400 端口。 根据上表，如果选择 "自动分配"，则每个只有1024。

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 出站规则和虚拟网络 NAT

Azure 负载均衡器出站规则和虚拟网络 NAT 是可用于从虚拟网络传出的选项。

有关出站规则的详细信息，请参阅 [出站规则](outbound-rules.md)。

有关 Azure 虚拟网络 NAT 的详细信息，请参阅 [什么是 Azure 虚拟网络 nat](../virtual-network/nat-overview.md)。

## <a name="constraints"></a>约束

*   如果收到或发送 **TCP RST** ，将在15秒后释放端口
*   如果收到或发送 **FINACK** ，将在240秒后释放端口
*   当连接空闲且未发送新数据包时，这些端口将在4到120分钟后释放。
  * 可以通过出站规则配置此阈值。
*   每个 IP 地址提供可用于 SNAT 的64000端口。
*   每个端口都可用于与目标 IP 地址建立 TCP 连接和 UDP 连接
  * 无论目标端口是否唯一，都需要 UDP SNAT 端口。 对于与目标 IP 的每个 UDP 连接，使用一个 UDP SNAT 端口。
  * 如果目标端口不同，TCP SNAT 端口可用于多个到相同目标 IP 的连接。
*   当后端实例用完了给定的 SNAT 端口时，将发生 SNAT 消耗。 负载均衡器仍可以具有未使用的 SNAT 端口。 如果后端实例的已使用 SNAT 端口超过其给定的 SNAT 端口，则无法建立新的出站连接。

## <a name="next-steps"></a>后续步骤

*   [排查因 SNAT 消耗而发生的出站连接故障](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [查看 SNAT 指标](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) ，熟悉筛选、拆分和查看它们的正确方法。

