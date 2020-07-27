---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc94f31887526f387413f78fe3270784a4e3bd88
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525140"
---
1. 在虚拟 WAN 的“门户”页上，选择“连接”部分中的“VPN 站点”以开打“VPN 站点”页 。
2. 在“VPN 站点”页上，单击“+创建站点”。

   ![基础知识](./media/virtual-wan-tutorial-site-include/basics.png "基础")
3. 在“创建 VPN 站点”页的“基本信息”选项卡上，填写以下字段 ：

    * **区域** - 之前称为位置。 这是要在其中创建此站点资源的位置。
    * **名称** - 本地站点的名称。
    * **设备供应商** - VPN 设备供应商的名称（例如：Citrix、Cisco、Barracuda）。 这有助于 Azure 团队更好地了解你的环境，以便将来添加更多的可用优化选项，或帮助你进行故障排除。
    * **边界网关协议** - 启用意味着来自站点的所有连接都将启用 BGP。 最后将从“链路”部分的 VPN 站点设置每条链路的 BGP 信息。 在虚拟 WAN 上配置 BGP 就等同于在 Azure 虚拟网络网关 VPN 上配置 BGP。 本地 BGP 对等节点地址不能与 VPN 到设备的公共 IP 地址或 VPN 站点的 VNet 地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。在表示该位置的相应 VPN 站点中指定此地址。 有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。 启用 VPN 站点 BGP 设置后，始终可以编辑 VPN 连接以更新其 BGP 参数（链路上的对等互连 IP 和 AS 编号）。
    * **专用地址空间** - 位于本地站点的 IP 地址空间。 发往此地址空间的流量将路由到本地站点。 如果没有为站点启用 BGP，则必需填写此字段。
    * **中心** - 希望站点连接到的中心。 站点只能连接到具有 VPN 网关的中心。 如果看不到中心，请先在相应的中心内创建 VPN 网关。
4. 选择“链路”以在分支添加物理链路的信息。 如果有虚拟 WAN 合作伙伴 CPE 设备，请检查该设备，查看是否将此信息作为从其系统设置的分支信息上传的一部分与 Azure 进行交换。

   ![链路](./media/virtual-wan-tutorial-site-include/links.png "链接")

    * **链路名称** - 要在 VPN 站点为物理链路提供的名称。 例如：mylink1。
    * **提供程序名称** - 物理链路在 VPN 站点的的名称。 示例：ATT、Verizon。
    * **速度** - 这是 VPN 设备在分支位置的速度。 示例：50 表示 VPN 设备在分支站点的速度为 50 Mbps。
    * **IP 地址/FQDN** - 使用此链路的本地设备的公共 IP 地址。 可以根据需要提供 ExpressRoute 后的本地 VPN 设备的专用 IP 地址。 你还可包括完全限定的域名。 例如 something.contoso.com。 应可从 VPN 网关解析 FQDN。 如果可通过 Internet 访问托管此 FQDN 的 DNS 服务器，此操作则是可行的。 如果同时指定了 IP 地址和 FQDN，将优先采用 IP 地址。

      >[!NOTE]
      >* 每个 FQDN 支持一个 IPv4 地址。 如果 FQDN 要被解析为多个 IP 地址，VPN 网关将从列表中选取第一个 IP4 地址。 目前不支持 IPv6 地址。
      >* VPN 网关维护一个 DNS 缓存，该缓存每 5 分钟刷新一次。 此网关仅尝试解析已断开连接的隧道的 FQDN。 重置网关或配置更改也可触发 FQDN 解析。
      >
5. 可以使用复选框删除或添加其他链路。 支持每个 VPN 站点四条链路。 例如，如果在分支位置有四个 ISP（Internet 服务提供商），则可以创建四条链路。 每个 ISP 一条链路，并为每条链路提供信息。
6. 填写完这些字段后，选择“查看 + 创建”来验证和创建站点。
7. 在“VPN 站点”页查看状态。 站点将转到“所需的连接”，因为该站点尚未连接到中心。
