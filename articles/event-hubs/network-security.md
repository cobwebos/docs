---
title: Azure 事件中心的网络安全
description: 本文介绍如何配置专用终结点的访问权限
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422770"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure 事件中心的网络安全 
本文介绍如何在 Azure 事件中心中使用以下安全功能： 

- 服务标记
- IP 防火墙规则
- 网络服务终结点
- 专用终结点（预览）


## <a name="service-tags"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址发生更改时自动更新服务标记，从而最大程度地降低频繁更新网络安全规则的复杂性。 有关服务标记的详细信息，请参阅[服务标记概述](../virtual-network/service-tags-overview.md)。

你可以使用服务标记来定义[网络安全组](../virtual-network/security-overview.md#security-rules) 或 [Azure 防火墙](../firewall/service-tags.md)上的网络访问控制。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应 "*源* " 或 " *目标* " 字段中指定服务标记名称（例如， **EventHub**），可以允许或拒绝相应服务的流量。

| 服务标记 | 目的 | 可以使用入站或出站？ | 可以是区域？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure 事件中心。 | 出站 | 是 | 是 |


## <a name="ip-firewall"></a>IP 防火墙 
默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问事件中心命名空间。 使用 IP 防火墙，你可以将其进一步限制为仅一组 IPv4 地址或[CIDR （无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的 ipv4 地址范围。

此功能在 Azure 事件中心应只能从某些已知站点进行访问的情况下很有用。 防火墙规则使你可以配置规则以接受来自特定 IPv4 地址的流量。 例如，如果将事件中心与[Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services)一起使用，则可以创建**防火墙规则**，以仅允许来自本地基础结构 IP 地址的流量。 

IP 防火墙规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与事件中心命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

有关详细信息，请参阅[如何为事件中心配置 IP 防火墙](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>网络服务终结点
通过将事件中心与[虚拟网络 (VNet) 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)相集成，可从绑定到虚拟网络的工作负荷（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

一旦配置为至少绑定到一个虚拟网络子网服务终结点，各自的事件中心命名空间就不再接受来自虚拟网络中的任何位置但获得授权的子网的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

然后，绑定到子网的工作负荷与相应的事件中心命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。 此行为有一个例外。 默认情况下，启用服务终结点将启用`denyall`与虚拟网络关联的[IP 防火墙](event-hubs-ip-filtering.md)中的规则。 可以在 IP 防火墙中添加特定的 IP 地址，以便能够访问事件中心公共终结点。 

> [!IMPORTANT]
> 事件中心的标准**** 和专用**** 层支持虚拟网络。 **基本**层不支持此方法。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

需要严格和事关的安全，以及虚拟网络子网在事关服务之间提供分段的解决方案，仍需要位于驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息服务提供了绝缘的通信路径，在这些路径中，消息在参与方之间过渡时甚至写入磁盘。 绑定到同一个事件中心实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

### <a name="bind-event-hubs-to-virtual-networks"></a>将事件中心绑定到虚拟网络

虚拟网络规则**** 是一种防火墙安全功能，用于控制是否允许 Azure 事件中心命名空间接受来自特定虚拟网络子网的连接。

将事件中心命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络的子网中创建一个**虚拟网络服务终结点**，并按[服务终结点概述](../virtual-network/virtual-network-service-endpoints-overview.md)一文中所述的那样为其启用 **。** 添加服务终结点后，使用虚拟网络规则将事件中心命名空间绑定到该终结点****。

虚拟网络规则是事件中心命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问事件中心命名空间。 事件中心本身从不建立出站连接，无需获取访问权限，因此永远不会通过启用此规则授予对子网的访问权限。

有关详细信息，请参阅[如何为事件中心配置虚拟网络服务终结点](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>专用终结点

使用[Azure 专用链接服务](../private-link/private-link-overview.md)，可以通过虚拟网络中的**专用终结点**访问 azure 服务（例如，Azure 事件中心、azure 存储和 Azure Cosmos DB）以及 azure 托管的客户/合作伙伴服务。

专用终结点是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

> [!NOTE]
> 只有**专用**层才支持此功能。 有关专用层的详细信息，请参阅[事件中心专用层的概述](event-hubs-dedicated-overview.md)。 
>
> 此功能目前处于**预览阶段**。 


有关详细信息，请参阅[如何为事件中心配置专用终结点](private-link-service.md)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [如何为事件中心配置 IP 防火墙](event-hubs-ip-filtering.md)
- [如何为事件中心配置虚拟网络服务终结点](event-hubs-service-endpoints.md)
- [如何为事件中心配置专用终结点](private-link-service.md)