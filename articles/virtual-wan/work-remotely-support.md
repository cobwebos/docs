---
title: Azure 虚拟 WAN 和远程工作
description: 本页介绍在新冠肺炎疫情期间如何利用 Azure 虚拟 WAN 来实现远程工作。
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753771"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure 虚拟 WAN 和远程工作支持

>[!NOTE]
>本文介绍了如何利用 Azure 虚拟 WAN、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作，并减少因 COVID-19 危机而面临的网络问题。
>

你是否在忙不迭地为远程用户提供连接？
你是否突然收到一大波用户的支持请求，远远超出了你原先的计划？
你是否需要用户在家中进行连接，并且不仅可以访问云，还可以访问本地？
你是否需要远程用户能够访问专用 WAN 后面的资源？
你是否需要用户访问云内资源而不必在区域之间建立连接？
这场蔓延全球的疫情给我们的生活带来了前所未有的变化，此时此际，无论你有何连接需求，Azure 虚拟 WAN 团队都将竭诚为你服务。

Azure 虚拟 WAN 是一个网络服务，其中整合了多种网络、安全和路由功能，提供单一操作界面。 这些功能包括分支连接（通过 SD-WAN 或 VPN CPE 等虚拟 WAN 合作伙伴设备的连接自动化）、站点到站点 VPN 连接、远程用户 VPN（点到站点）连接、专用 (ExpressRoute) 连接、云内连接（虚拟网络的可传递连接）、VPN ExpressRoute 互连、路由、Azure 防火墙、专用连接加密等。无需所有这些用例即可开始使用虚拟 WAN。 可从一个用例开始，并随着情况变化对网络进行调整。

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan1.png)

话题转到远程用户，让我们看看启动并运行网络所需具备的条件：

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>设置远程用户连接

通过 IPsec/IKE (IKEv2) 或 OpenVPN 连接可以连接到 Azure 中的资源。 此类连接要求为远程用户配置一个 VPN 客户端。 该客户端可以是 [Azure VPN 客户端](https://go.microsoft.com/fwlink/?linkid=2117554)或 OpenVPN 客户端，也可以是任何支持 IKEv2 的客户端。 有关详细信息，请参阅[创建点到站点连接](virtual-wan-point-to-site-portal.md)。

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>从远程用户到本地的连接

可以使用以下两个选项：

* 设置与任何现有 VPN 设备的站点到站点连接。 将 IPsec VPN 设备连接到 Azure 虚拟 WAN 中心时，点到站点用户 VPN（远程用户）和站点到站点 VPN 会自动互连。 有关如何在本地 VPN 设备与 Azure 虚拟 WAN 之间设置站点到站点 VPN 的详细信息，请参阅[使用虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)。

* 将 ExpressRoute 线路连接到虚拟 WAN 中心。 连接 ExpressRoute 线路需要在虚拟 WAN 中部署 ExpressRoute 网关。 部署后，点到站点用户 VPN 和 ExpressRoute 用户会自动互连。 若要创建 ExpressRoute 连接，请参阅[使用虚拟 WAN 创建 ExpressRoute 连接](virtual-wan-expressroute-portal.md)。 可以使用现有 ExpressRoute 线路连接到 Azure 虚拟 WAN。

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>现有的基本虚拟 WAN 客户

基本虚拟 WAN 仅提供站点到站点 VPN。 若要让远程用户成功连接，需要将虚拟 WAN 升级到标准虚拟 WAN。 有关升级虚拟 WAN 的步骤，请参阅[从基本虚拟 WAN 升级到标准虚拟 WAN](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>其他信息

虚拟 WAN 在每个区域/位置支持一个中心。 有关位置信息，请参阅[虚拟 WAN 合作伙伴和位置](virtual-wan-locations-partners.md)一文。 每个中心最多支持 10,000 个远程用户连接、1,000 个分支连接、四条 ExpressRoute 线路和最多 500 个虚拟网络连接。 增加远程用户时，如有任何疑问，请随时向 azurevirtualwan@microsoft.com 发送电子邮件以寻求帮助。 如需技术支持，请确保在 Azure 门户中开具支持票证，我们将随时提供帮助。

## <a name="faq"></a><a name="faq"></a>常见问题解答

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)