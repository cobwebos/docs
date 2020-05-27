---
title: Azure 中继的网络安全性
description: 本文介绍如何配置专用终结点的访问权限
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211009"
---
# <a name="network-security-for-azure-relay"></a>Azure 中继的网络安全性 
本文介绍如何将以下安全功能与 Azure 中继配合使用： 

- IP 防火墙规则（预览版）
- 专用终结点（预览版）

> [!NOTE]
> Azure 中继不支持网络服务终结点。 


## <a name="ip-firewall"></a>IP 防火墙 
默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问中继命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

在仅应从某些知名站点访问 Azure 中继的情况下，此功能很有用。 可以通过防火墙规则来配置规则，以便接受来自特定 IPv4 地址的流量。 例如，如果将中继与 [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services) 配合使用，则可创建防火墙规则，仅允许来自本地基础结构 IP 地址的流量。 

IP 防火墙规则应用于中继命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与中继命名空间的允许 IP 规则不匹配，系统会拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

有关详细信息，请参阅[如何为中继命名空间配置 IP 防火墙](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>专用终结点

使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如，Azure 中继、Azure 服务总线、Azure 事件中心、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户/合作伙伴服务。 有关详细信息，请参阅[什么是 Azure 专用链接（预览版）？](../private-link/private-link-overview.md)

专用终结点是一个网络接口，它允许在虚拟网络中运行的工作负荷以私密方式安全地连接到具有专用链接资源的服务（例如，中继命名空间）。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute、VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以通过允许连接到特定的 Azure 中继命名空间来提供访问控制的粒度级别。

> [!NOTE]
> 此功能目前为预览版。 

有关详细信息，请参阅[如何配置专用终结点](private-link-service.md)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [如何配置 IP 防火墙](ip-firewall-virtual-networks.md)
- [如何配置专用终结点](private-link-service.md)