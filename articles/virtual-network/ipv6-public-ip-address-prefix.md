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
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 2d00ccdeb89ba5d983e4a3e089e78a8d748e4092
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597950"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>保留的公共 IPv6 地址前缀（预览）

在 Azure 中，双堆栈（IPv4 + IPv6）虚拟网络（VNet）和虚拟机（Vm）在默认情况下是安全的，因为它们没有 Internet 连接。 可以轻松地将 IPv6 Internet 连接添加到 Azure 负载均衡器和 Vm，其中包含从 Azure 获取的公共 IPv6 地址。

保留的任何公共 Ip 均与所选的 Azure 区域和 Azure 订阅相关联。 可以在订阅中的任何 Azure 负载平衡器或 Vm 之间移动保留（静态） IPv6 公共 IP。 你可以完全取消 IPv6 公共 IP 的关联，并在准备就绪时将其保留供你使用。

> [!WARNING]
> 请小心不要意外删除公共 IP 地址。 删除公共 IP 会将其从你的订阅中删除，你将无法恢复它（甚至在 Azure 支持的帮助之外）。

除了保留单个 IPv6 地址外，你还可以保留一个连续的 Azure IPv6 地址范围（称为 IP 前缀）供你使用。  与单个 IP 地址类似，保留的前缀与你选择的 Azure 区域和 Azure 订阅相关联。 保留可预测的连续地址范围有很多用途。 例如，你可以极大地简化公司和客户的 Azure 托管应用程序的 IP*允许列表*，因为你的静态 IP 范围可以轻松地加入到本地防火墙中。  您可以根据需要从您的 IP 前缀创建单个公共 Ip，并在删除这些公共 Ip 时，它们将*返回*到保留范围，以便以后可以重复使用。 在删除前缀之前，IP 前缀中的所有 IP 地址都是为独占使用而保留的。

> [!Important]
> Azure 虚拟网络的 IPv6 目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="ipv6-prefix-sizes"></a>IPv6 前缀大小
以下公共 IP 前缀大小可用：

-  最小 IPv6 前缀大小：/127 = 2 地址
-  最大 IPv6 前缀大小：/124 = 16 个地址

前缀大小指定为无类别的域间路由（CIDR）掩码大小。 例如，/128 的掩码表示单个 IPv6 地址，因为 IPv6 地址由128位组成。

## <a name="pricing"></a>价格
 
有关使用 Azure 公共 ip、单个 IP 地址和 IP 范围的成本，请参阅[公共 Ip 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="limitations"></a>限制
仅具有 "动态" 分配的基本公共 Ip 支持 IPv6，这意味着，如果在 Azure 中删除和重新部署应用程序（VM 或负载均衡器），IPv6 地址会发生更改。 只有标准 IPv6 公共 IP 支持动态和静态（保留）分配。

作为最佳做法，我们建议为 IPv6 应用程序使用标准公共 Ip 和标准负载均衡器。

## <a name="next-steps"></a>后续步骤
- 保留公共[IPv6 地址前缀](ipv6-reserve-public-ip-address-prefix.md)。
- 了解有关[IPv6 地址](ipv6-overview.md)的详细信息。
- 了解如何在 Azure 中[创建和使用公共 ip](virtual-network-public-ip-address.md) （IPv4 和 IPv6）。
