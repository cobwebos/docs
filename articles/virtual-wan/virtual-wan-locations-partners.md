---
title: Azure 虚拟 WAN 合作伙伴位置 | Microsoft Docs
description: 本文包含 Azure 虚拟 WAN 合作伙伴和中心位置的列表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: afa808c95c88184fb531e858fbfe6d102a6f1f08
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157153"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虚拟 WAN 合作伙伴和虚拟中心位置

本文提供了有关连接到虚拟中心的虚拟 WAN 支持区域和合作伙伴的信息。

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 这可以手动完成，也可以通过虚拟 WAN 伙伴使用提供商设备完成。 使用合作伙伴设备，您可以使用、简化连接性和配置管理。

本地设备到虚拟中心的连接将自动建立。 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含不同的服务终结点，可从本地网络 (vpnsite) 建立连接。 每个区域只能有一个中心。

## <a name="automation"></a>来自连接伙伴的自动化

连接到 Azure 虚拟 WAN 的设备具有内置的自动化连接功能。 这通常在设备管理 UI（或同等位置）中设置，该 UI 设置 VPN 分支设备到 Azure 虚拟中心 VPN 终结点（VPN 网关）之间的连接和配置管理。

以下高级自动化在设备控制台/管理中心中设置：

* 设备访问 Azure 虚拟 WAN 资源组的相应权限
* 将分支设备上传到 Azure 虚拟 WAN
* 自动下载 Azure 连接信息
* 本地分支设备的配置 

某些连接合作伙伴可以扩展自动化，包括创建 Azure 虚拟中心 VNet 和 VPN 网关。 若想了解有关自动化的详细信息，请参阅[配置自动化 - WAN 合作伙伴](virtual-wan-configure-automation-providers.md)。

## <a name="partners"></a>通过合作伙伴建立连接

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

以下合作伙伴计划在我们的未来发展路线图： Arista、Aruba HPE、Cisco Systems、F5 网络、开放系统、Oracle SD-WAN、SharpLink 和 VMWare Velocloud。

## <a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

* 有关如何自动连接到 Azure 虚拟 WAN 的详细信息，请参阅[虚拟 WAN 合作伙伴 - 如何实现自动化](virtual-wan-configure-automation-providers.md)。
