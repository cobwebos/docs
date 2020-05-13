---
title: Azure 负载均衡器的概念
description: 概述了 Azure 负载均衡器的概念
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 2e2e11e1dc16fd99a82bc2fbdc7b72399bc6ae97
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871920"
---
# <a name="azure-load-balancer-concepts"></a>Azure 负载均衡器的概念

负载均衡器针对 UDP 和 TCP 应用程序提供了多种功能。 

## <a name="load-balancing-algorithm"></a>负载均衡算法
你可以创建负载均衡规则，以将来自前端的流量分配到后端池。 Azure 负载均衡器使用哈希算法来分配入站流（非字节）。 负载均衡器会重写发往后端池实例的流的标头。 当运行状况探测指示后端终结点正常时，可以使用一个服务器来接收新流量。

默认情况下，负载均衡器使用五元组哈希。

哈希包括：

- **源 IP 地址**
- **源端口**
- **目标 IP 地址**
- **目标端口**
- **用于将流映射到可用服务器的 IP 协议号**

与源 IP 地址的关联是使用二元组或三元组哈希创建的。 同一个流的数据包将会抵达负载均衡前端后面的同一实例。 

当客户端从同一源 IP 启动新流时，源端口将会更改。 因此，五元组哈希可能会导致流量定向到不同的后端终结点。
有关详细信息，请参阅[配置 Azure 负载均衡器的分配模式](./load-balancer-distribution-mode.md)。

下图显示了基于哈希的分配：

  ![基于哈希的分发](./media/load-balancer-overview/load-balancer-distribution.png)

  图：  基于哈希的分发

## <a name="application-independence-and-transparency"></a>应用程序独立性和透明度

负载均衡器不直接与 TCP、UDP 或应用程序层进行交互。 可以支持任何 TCP 或 UDP 应用程序方案。 负载均衡器不会关闭或发起流，也不会与流的有效负载进行交互。 负载均衡器不提供任何应用程序层网关功能。 协议握手始终直接在客户端与后端池实例之间进行。 对入站流做出的响应始终是来自虚拟机的响应。 当流抵达虚拟机时，也会保留原始的源 IP 地址。

* 每个终结点由某个 VM 应答。 例如，TCP 握手在客户端与选定的后端 VM 之间发生。 对前端请求做出的响应是后端 VM 生成的响应。 成功验证与前端的连接后，将会验证与至少一个后端虚拟机的整个连接。
* 应用程序有效负载对于负载均衡器是透明的。 可以支持任何 UDP 或 TCP 应用程序。
* 由于负载均衡器不会与 TCP 有效负载进行交互并提供了 TLS 卸载，因此你可以构建全面的加密方案。 使用负载均衡器可通过在 VM 自身上终止 TLS 连接来实现 TLS 应用程序的大规模横向扩展。 例如，将会根据添加到后端池的 VM 类型和数目限制 TLS 会话密钥容量。

## <a name="load-balancer-terminology"></a>负载均衡器术语
| 概念 | 它意味着什么？ | 详细文档 |
| ---------- | ---------- | ----------|
出站连接 | 从后端池到公共 IP 的流将映射到前端。 Azure 通过负载均衡出站规则将出站连接转换为公共前端 IP 地址。 此配置具有以下优点。 可以轻松地对服务进行升级和灾难恢复操作，因为前端可以动态映射到服务的其他实例。 简化了访问控制列表 (ACL) 管理。 以前端 IP 表示的 ACL 不会随着服务的缩放或重新部署而更改。 将出站连接转换为较小数量的 IP 地址而不是计算机，可以减少实施安全收件人列表的负担。| 若要详细了解源网络地址转换 (SNAT) 和 Azure 负载均衡器，请参阅 [SNAT 和 Azure 负载均衡器](load-balancer-outbound-connections.md#snat)。
可用性区域 | 在提供可用性区域的区域中，标准负载均衡器可以支持更多功能。 这些功能是对标准负载均衡器提供的所有功能的补充。  可用性区域配置适用于以下两种类型的标准负载均衡器：“公共”和“内部”。通过在所有区域中同时使用专用基础结构，区域冗余的前端将会在发生区域故障后继续正常工作。 此外，可以保证特定区域的前端。 区域前端由单个区域中的专用基础结构提供服务。 后端池可使用跨区域负载均衡。 虚拟网络中的任何虚拟机资源都可以是后端池的一部分。基本负载均衡器不支持区域。| 有关详细信息，请查看[有关可用性区域相关功能的详细讨论](load-balancer-standard-availability-zones.md)和[可用性区域概述](../availability-zones/az-overview.md)。
| HA 端口 | 你可以配置 HA 端口负载均衡规则，让应用程序可缩放，并且变得高度可靠。 这些规则在内部负载均衡器前端 IP 的短生存期端口上为每个流提供负载均衡。 无法或不需要指定各个端口时，该功能很有用。 HA 端口规则允许创建主动-被动或主动-主动 n+1 方案。 这些方案适用于网络虚拟设备以及任何需要大范围入站端口的应用程序。 可以使用运行状况探测来确定哪些后端应当接收新流。  可使用网络安全组模拟端口范围方案。 基本负载均衡器不支持 HA 端口。 | 请查看[有关 HA 端口的详细讨论](load-balancer-ha-ports-overview.md)
| 多个前端 | 负载均衡器支持具有多个前端的多个规则。  标准负载均衡器将此功能扩展到了出站方案。 出站规则与入站规则相反。 出站规则创建出站连接的关联。 标准负载均衡器通过负载均衡规则使用与虚拟机资源关联的所有前端。 此外，负载均衡规则中的参数允许为了出站连接取消负载均衡规则，并允许选择特定前端（包括无前端）。 与之相对的是，基本负载均衡器随机选择单个前端。 无法控制选择哪一个前端。|


## <a name="limitations"></a><a name = "limitations"></a>限制

- 负载均衡器规则不能跨越两个虚拟网络。  前端及其后端实例必须位于同一个虚拟网络中。  

- 没有虚拟网络和其他 Microsoft 平台服务的 Web 辅助角色只能从内部标准负载均衡器后面的实例进行访问。 请勿依赖于这种访问方式，因为相应的服务本身或底层平台可能会在不通知的情况下进行更改。 如果在使用标准内部负载均衡器时需要出站连接，必须配置[出站连接](load-balancer-outbound-connections.md)。

- 负载均衡器针对特定的 TCP 或 UDP 协议提供了负载均衡和端口转发。 负载均衡规则和入站 NAT 规则支持 TCP 和 UDP，但不支持其他 IP 协议（包括 ICMP）。

- 从后端 VM 到内部负载均衡器前端的出站流将会失败。

- 负载均衡规则不支持转发 IP 片段。 负载均衡规则不支持 UDP 和 TCP 数据包的 IP 片段。 HA 端口负载均衡规则可用于转发现有 IP 片段。 有关详细信息，请参阅[高可用性端口概述](load-balancer-ha-ports-overview.md)。

## <a name="next-steps"></a>后续步骤

- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)以开始使用负载均衡器：在已安装自定义 IIS 扩展的情况下创建 VM，然后对 VM 之间的 Web 应用进行负载均衡。
- 了解 [Azure 负载均衡器出站连接](load-balancer-outbound-connections.md)。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 详细了解[网络安全组](../virtual-network/security-overview.md)。
