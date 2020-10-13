---
title: Azure 虚拟网络中的公共 IPv6 地址前缀
titlesuffix: Azure Virtual Network
description: 了解 Azure 虚拟网络中的公共 IPv6 地址前缀。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ff33bf771065e7af209934a5c54b6f8f2e34835e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666777"
---
# <a name="reserved-public-ipv6-address-prefix"></a>保留的公共 IPv6 地址前缀

在 Azure 中，双堆栈 (IPv4 + IPv6) 虚拟网络 (VNet) 和虚拟机 (VM) 默认是安全的，因为它们不会建立 Internet 连接。 可以使用从 Azure 获取的公共 IPv6 地址，轻松将 IPv6 Internet 连接添加到 Azure 负载均衡器和 VM。

保留的任何公共 IP 均与所选 Azure 区域和 Azure 订阅相关联。 可以在订阅中的任何 Azure 负载均衡器或 VM 之间移动保留的（静态）IPv6 公共 IP。 可以完全取消关联 IPv6 公共 IP，然后，该 IP 将会保留供你随时使用。

> [!WARNING]
> 请小心不要意外删除公共 IP 地址。 删除某个公共 IP 会将其从订阅中删除，而且无法予以恢复（甚至在 Azure 支持人员的帮助之下也不能恢复）。

除了保留单个 IPv6 地址以外，还可以保留连续的 Azure IPv6 地址范围（称作 IP 前缀）供你使用。  与单个 IP 地址类似，保留的前缀与所选 Azure 区域和 Azure 订阅相关联。 保留可预测的连续地址范围有很多好处。 例如，你可以极大地简化公司和客户对 Azure 托管应用程序的 IP *筛选* ，因为你的静态 IP 范围可以轻松地加入到本地防火墙中。  可以根据需要基于 IP 前缀创建单个公共 IP，删除这些公共 IP 时，它们将返回到保留的范围，日后可供重复使用。  IP 前缀中的所有 IP 地址保留供你独用，直到删除该前缀。



## <a name="ipv6-prefix-sizes"></a>IPv6 前缀大小
可使用以下公共 IP 前缀：

-  最小 IPv6 前缀大小：/127 = 2 个地址
-  最大 IPv6 前缀大小：/124 = 16 个地址

前缀大小指定为无类域间路由 (CIDR) 掩码大小。 例如，掩码 /128 表示单个 IPv6 地址，因为 IPv6 地址由 128 位组成。

## <a name="pricing"></a>定价
 
有关 Azure 公共 IP（单个 IP 地址和 IP 范围）的使用成本，请参阅[公共 IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
只有采用“动态”分配的基本公共 IP 才支持 IPv6，这意味着，如果在 Azure 中删除再重新部署应用程序（VM 或负载均衡器），IPv6 地址会发生变化。 标准 IPv6 公共 IP 仅支持静态（保留）分配，不过，标准内部负载均衡器也可以支持从其分配到的子网内部进行动态分配。  

作为最佳做法，我们建议对 IPv6 应用程序使用标准公共 IP 和标准负载均衡器。

## <a name="next-steps"></a>后续步骤
- 保留公共 [IPv6 地址前缀](ipv6-reserve-public-ip-address-prefix.md)。
- 详细了解 [IPv6 地址](ipv6-overview.md)。
- 了解[如何在 Azure 中创建和使用公共 IP](virtual-network-public-ip-address.md)（IPv4 和 IPv6）。
