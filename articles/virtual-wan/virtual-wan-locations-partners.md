---
title: Azure 虚拟 WAN 合作伙伴位置 | Microsoft Docs
description: 本文包含 Azure 虚拟 WAN 的合作伙伴和中心位置的列表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 985823e5aa806244596277c982cc129554d890f2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489031"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>虚拟 WAN 合作伙伴和虚拟中心位置

本文提供有关虚拟 WAN 针对连接到虚拟中心所支持的区域以及合作伙伴的信息。

Azure Virtual WAN 是一种网络服务，它通过 Azure 提供经优化、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可以手动完成此操作，也可以通过虚拟 WAN 合作伙伴使用提供商设备完成此操作。 使用合作伙伴设备可以降低操作难度、简化连接和进行配置管理。

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

以下合作伙伴在我们的计划中针对不久的未来进行了预定： Arista、F5 网络、Velocloud。

## <a name="locations"></a>位置

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。

* 有关如何自动连接到 Azure 虚拟 WAN 的详细信息，请参阅[虚拟 WAN 合作伙伴 - 如何实现自动化](virtual-wan-configure-automation-providers.md)。
