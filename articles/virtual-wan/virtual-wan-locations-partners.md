---
title: Azure 虚拟 WAN 合作伙伴和位置 |Microsoft Docs
description: 本文包含 Azure 虚拟 WAN 的合作伙伴和中心位置的列表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 928a68cff5dc8043e69c25be3dcfa3510a7d3a2a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267288"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虚拟 WAN 合作伙伴和虚拟中心位置

本文提供有关虚拟 WAN 针对连接到虚拟中心所支持的区域以及合作伙伴的信息。

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可以手动完成此操作，也可以通过虚拟 WAN 合作伙伴使用提供商设备完成此操作。 使用合作伙伴设备可以降低操作难度、简化连接和进行配置管理。

本地设备到虚拟中心的连接将自动建立。 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 每个区域只能有一个中心。

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>从合作伙伴分支 IPSec 连接自动化

连接到 Azure 虚拟 WAN 的设备具有内置的自动化连接功能。 这通常在设备管理 UI（或同等位置）中设置，该 UI 设置 VPN 分支设备到 Azure 虚拟中心 VPN 终结点（VPN 网关）之间的连接和配置管理。

以下高级自动化在设备控制台/管理中心中设置：

* 设备访问 Azure 虚拟 WAN 资源组的相应权限
* 将分支设备上传到 Azure 虚拟 WAN
* 自动下载 Azure 连接信息
* 本地分支设备的配置 

某些连接合作伙伴可以扩展自动化，包括创建 Azure 虚拟中心 VNet 和 VPN 网关。 若要了解有关自动化的详细信息，请参阅[适用于虚拟 WAN 合作伙伴的自动化指南](virtual-wan-configure-automation-providers.md)。

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>分支 IPSec 连接伙伴

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

以下合作伙伴在我们的计划中针对不久的未来进行了预定：128技术、Arista、Cisco Systems (Viptela) 、F5 网络、Oracle SD-WAN 和 SharpLink。

## <a name="partners-with-integrated-virtual-hub-offerings"></a>具有集成虚拟中心产品/服务的合作伙伴
除了具有自动分支机构 IPSec 连接之外，某些合作伙伴还提供可直接集成到 Azure 虚拟 WAN 集线器 ** (nva) 的网络虚拟设备 ** 。  这允许客户将其分支连接终止为虚拟中心中兼容的第三方设备。  

在虚拟 WAN 中心提供 NVA 的合作伙伴必须：

* 已实现来自其分支设备的 IPSec 连接自动化，并已将其 NVA 产品载入到 Azure 虚拟 WAN 中心。
* 在 Azure Marketplace 中提供现有网络虚拟设备产品/服务。

如果你是合作伙伴，并对虚拟中心产品中的托管 NVA 有疑问，请通过将电子邮件发送到来联系我们 vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>集成的虚拟中心 NVA 合作伙伴
这些合作伙伴现在提供了 **托管应用程序** 产品/服务，可将其部署到虚拟 WAN 中心。

|合作伙伴|配置/操作方法/部署指南|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN 部署指南](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco 云服务路由器 (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| [Cisco 云服务路由器 (CSR) VWAN 部署指南]()

在不久的将来，我们会将以下合作伙伴预定给虚拟中心的 NVA： Citrix，反之亦然。

## <a name="locations"></a><a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

* 有关如何自动连接到 Azure 虚拟 WAN 的详细信息，请参阅[适用于虚拟 WAN 合作伙伴的自动化指南](virtual-wan-configure-automation-providers.md)。
