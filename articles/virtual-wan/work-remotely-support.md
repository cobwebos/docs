---
title: Azure 虚拟 WAN 和远程工作
description: 本页介绍如何利用 Azure 虚拟 WAN 启用由于 COVID-19 大流行而远程工作。
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337129"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure 虚拟 WAN 和支持远程工作

>[!NOTE]
>本文介绍如何利用 Azure 虚拟 WAN、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作并缓解因 COVID-19 危机而面临的网络问题。
>

您是否争先恐后地为远程用户提供连接？
您是否突然看到需要支持超出计划的用户激增？
您是否需要用户从家里进行连接，不仅访问云，而且能够到达本地？
您是否需要远程用户能够访问专用广域网背后的资源？
您是否需要用户访问云内部资源，而无需设置区域之间的连接？
由于此全球大流行在我们周围创造了前所未有的变化，Azure 虚拟 WAN 团队在这里为您满足您的连接需求。

Azure 虚拟 WAN 是一种网络服务，它汇集了许多网络、安全和路由功能，以提供单个操作接口。 这些功能包括分支连接（通过虚拟 WAN 合作伙伴设备（如 SD-WAN 或 VPN CPE）的连接自动化）、站点到站点 VPN 连接、远程用户 VPN（点对点）连接、专用（快速路由）连接、云内连接（虚拟网络的传输连接）、VPN 快速路由互连、路由、Azure 防火墙、专用连接加密等。您不必拥有所有这些用例，就无需开始使用虚拟 WAN。 您可以开始使用一个用例，并随着网络的发展进行调整。

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan1.png)

现在，在谈论远程用户，让我们来看看需要什么来启动和运行您的网络：

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>设置远程用户连接

通过 IPsec/IKE (IKEv2) 或 OpenVPN 连接可以连接到 Azure 中的资源。 这种类型的连接需要为远程用户配置 VPN 客户端。 此客户端可以是[Azure VPN 客户端](https://go.microsoft.com/fwlink/?linkid=2117554)或 OpenVPN 客户端或支持 IKEv2 的任何客户端。 有关详细信息，请参阅[创建点到站点连接](virtual-wan-point-to-site-portal.md)。

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>从远程用户到本地的连接

这里有两个选项：

* 与任何现有 VPN 设备设置站点到站点的连接。 将 IPsec VPN 设备连接到 Azure 虚拟广域网中心时，点对点用户 VPN（远程用户）和站点到站点 VPN 之间的互连是自动的。 有关如何从本地 VPN 设备到 Azure 虚拟 WAN 设置站点到站点 VPN 的详细信息，请参阅[使用虚拟 WAN 创建站点到站点的连接](virtual-wan-site-to-site-portal.md)。

* 将快速路由电路连接到虚拟广域网集线器。 连接快速路由电路需要在虚拟广域网中部署快速路由网关。 部署一个站点用户 VPN 和 ExpressRoute 用户之间的互连是自动的。 要创建快速路由连接，请参阅[使用虚拟 WAN 创建快速路由连接](virtual-wan-expressroute-portal.md)。 您可以使用现有的 ExpressRoute 电路连接到 Azure 虚拟广域网。

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>现有基本虚拟广域网客户

基本虚拟 WAN 仅提供站点到站点 VPN。 为了使远程用户进行连接，您需要将虚拟 WAN 升级到标准虚拟广域网。 有关升级虚拟 WAN 的步骤，请参阅[将虚拟 WAN 从基本 WAN 升级到标准](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>其他信息

虚拟 WAN 支持每个区域/位置一个集线器。 有关位置信息，请参阅[虚拟 WAN 合作伙伴和位置](virtual-wan-locations-partners.md)一文。 每个集线器支持多达 10，000 个远程用户连接、1，000 个分支连接、4 个 ExpressRoute 电路和多达 500 个虚拟网络连接。 在扩展远程用户时，如果您有任何问题，请毫不犹豫地向 发送电子邮件寻求帮助azurevirtualwan@microsoft.com。 如果需要技术支持，请确保从 Azure 门户打开支持票证，并且帮助即将提供。

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)