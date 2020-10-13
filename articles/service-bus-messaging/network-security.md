---
title: Azure 服务总线的网络安全性
description: 本文介绍网络安全功能，如服务标记、IP 防火墙规则、服务终结点和专用终结点。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fb21c8beb6d48ecab04917525011cc4762c46ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766403"
---
# <a name="network-security-for-azure-service-bus"></a>Azure 服务总线的网络安全性 
本文介绍如何在 Azure 服务总线中使用以下安全功能： 

- 服务标记
- IP 防火墙规则
- 网络服务终结点
- 专用终结点


## <a name="service-tags"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。 有关服务标记的详细信息，请参阅[服务标记概述](../virtual-network/service-tags-overview.md)。

可以在[网络安全组](../virtual-network/security-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，请使用服务标记代替特定 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名（例如，ServiceBus），可以允许或拒绝相应服务的流量    。

| 服务标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **服务总线** | 使用高级服务层级的 Azure 服务总线流量。 | 出站 | 是 | “是” |


> [!NOTE]
> 只能将服务标记用于“高级”命名空间。 如果使用“标准”命名空间，请使用运行以下命令时看到的 IP 地址：`nslookup <host name for the namespace>`。 例如：`nslookup contosons.servicebus.windows.net`。 

## <a name="ip-firewall"></a>IP 防火墙 
默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问服务总线命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

在仅应从某些知名站点访问 Azure 服务总线的情况下，此功能很有用。 可使用防火墙规则配置相关规则，以接受源自特定 IPv4 地址的流量。 例如，如果将服务总线与 [Azure Express Route][express-route] 配合使用，则可创建防火墙规则  ，只允许来自本地基础结构 IP 地址或公司 NAT 网关地址的流量。 

IP 防火墙规则应用于服务总线命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与服务总线命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

有关详细信息，请参阅[如何为服务总线命名空间配置 IP 防火墙](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>网络服务终结点
通过将服务总线与[虚拟网络 (VNet) 服务终结点](service-bus-service-endpoints.md)集成可从绑定到虚拟网络的工作负荷（如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

配置为绑定到至少一个虚拟网络子网服务终结点后，相应的服务总线命名空间将不再接受授权虚拟网络以外的任何位置的流量。 从虚拟网络的角度来看，通过将服务总线命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。

然后，绑定到子网的工作负荷与相应的服务总线命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。

> [!IMPORTANT]
> 虚拟网络仅在[高级层](service-bus-premium-messaging.md)服务总线命名空间中受支持。
> 
> 在服务总线中使用 VNet 服务终结点时，不应在将标准层服务总线命名空间和高级层服务总线命名空间混合使用的应用程序中启用这些终结点。 原因是标准层不支持 VNet。 此终结点仅限于高级层命名空间。

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

对于需要严格和隔离安全性的解决方案和虚拟网络子网在其中的隔离服务之间提供分段的解决方案，它们通常仍然需要驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息传递服务提供完全隔离的通信路径，其中消息在各方之间转换时会以平均方式写入磁盘。 绑定到同一个服务总线实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

### <a name="bind-service-bus-to-virtual-networks"></a>将服务总线绑定到虚拟网络

虚拟网络规则是一种防火墙安全功能，用于控制是否允许 Azure 服务总线服务器接受来自特定虚拟网络子网的连接  。

将服务总线命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络子网上创建“虚拟网络服务终结点”，并按照[服务终结点概述](service-bus-service-endpoints.md)中的说明为“Microsoft.ServiceBus”启用该终结点 。 添加服务终结点后，使用虚拟网络规则将服务总线命名空间绑定到该终结点。

虚拟网络规则是服务总线命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问服务总线命名空间。 服务总线本身永远不会建立出站连接，不需要获得访问权限，因此永远不会通过启用此规则来授予对子网的访问权限。

有关详细信息，请参阅[如何为服务总线命名空间配置虚拟网络服务终结点](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>专用终结点

使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure 服务总线、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户服务/合作伙伴服务。

专用终结点是一个网络接口，可以通过专用且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[什么是 Azure 专用链接？](../private-link/private-link-overview.md)

> [!NOTE]
> 通过 Azure 服务总线高级层支持此功能。 有关高级层的详细信息，请参阅[服务总线高级和标准消息传送层](service-bus-premium-messaging.md)。


有关详细信息，请参阅 [如何为服务总线命名空间配置专用终结点](private-link-service.md)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [如何为服务总线命名空间配置 IP 防火墙](service-bus-ip-filtering.md)
- [如何为服务总线命名空间配置虚拟网络服务终结点](service-bus-service-endpoints.md)
- [如何配置服务总线命名空间的专用终结点](private-link-service.md)
