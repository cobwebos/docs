---
title: Azure 公共 IP 地址前缀
description: 了解 Azure 公共 IP 地址前缀的涵义以及如何借助该前缀向资源分配可预测的公共 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432586"
---
# <a name="public-ip-address-prefix"></a>公共 IP 地址前缀

公共 IP 地址前缀是 Azure 中保留的 IP 地址范围。 Azure 根据你指定的数量为你的订阅提供了一个连续的地址范围。 

如果不熟悉公共地址，请参阅[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。

公共 IP 地址是从各 Azure 区域的地址池分配的。 可[下载](https://www.microsoft.com/download/details.aspx?id=56519) Azure 在每个区域使用的范围列表。 例如，40.121.0.0/16 是 Azure 在美国东部区域中使用的 100 多个范围之一。 该范围包括从 40.121.0.1 到 40.121.255.254 的可用地址。

可通过指定一个名称和希望前缀包括的地址数，在 Azure 区域和订阅中创建公共 IP 地址前缀。 

公共 IP 地址范围分配有你选择的前缀。 如果创建了/28 前缀，Azure 将从其范围之一提供16个 ip 地址。

除非创建范围，否则你不会知道 Azure 将分配哪个范围给你，但这些地址是连续的。 

公共 IP 地址前缀有费用，有关详细信息，请参阅 [公共 ip 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="why-create-a-public-ip-address-prefix"></a>为什么要创建公共 IP 地址前缀？

创建公共 IP 地址资源时，Azure 会从该区域中使用的任何范围分配一个可用的公共 IP 地址。 

在 Azure 分配 IP 地址之前，你不会知道确切的 IP。 创建允许特定 IP 地址的防火墙规则时，此过程可能会产生问题。 对于添加的每个 IP 地址，必须添加相应的防火墙规则。

从公共 IP 地址前缀向资源分配地址时，无需防火墙规则更新。 整个范围将添加到规则。

## <a name="benefits"></a>好处

- 从已知范围创建公共 IP 地址资源。
- 防火墙规则配置，其中包含当前已分配的公共 IP 地址以及尚未分配的地址。 此配置无需更改防火墙规则，因为向新资源分配 IP 地址。
- 可创建的默认范围大小为 /28 或 16 个 IP 地址。
- 对于可以创建的范围数没有限制。 可以在 Azure 订阅中拥有的最大静态公共 IP 地址数有限制。 创建的范围数不能超过订阅中的可用静态公共 IP 地址数。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用来自前缀的地址创建的地址可以分配到可向其分配公共 IP 地址的任何 Azure 资源。
- 你可以轻松地查看在该范围内给定的和不给定的 IP 地址。

## <a name="scenarios"></a>方案
可将以下资源关联到来自前缀的静态公共 IP 地址：

|资源|方案|步骤|
|---|---|---|
|虚拟机| 在将 IP 地址添加到防火墙中的允许列表时，可以将公共 ip 从一个前缀关联到 Azure 中的虚拟机，从而减少管理开销。 可以使用单个防火墙规则添加整个前缀。 缩放 Azure 中的虚拟机时，可关联来自同一前缀的 IP，从而节省成本、时间和管理开销。| 将来自某个前缀的 IP 关联到虚拟机： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. [将 IP 关联到虚拟机的网络接口。](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> 也可以[将 IP 关联到虚拟机规模集](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)。
| 标准负载均衡器 | 将公共 Ip 从前缀关联到前端 IP 配置或负载均衡器的出站规则，可确保 Azure 公共 IP 地址空间的简化。 通过从连续 IP 地址的范围中整理出站连接，简化你的方案。 | 若要将 Ip 从前缀关联到负载均衡器，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. 创建负载均衡器时，选择或更新在上面的步骤2中创建的 IP 作为负载均衡器的前端 IP。 |
| Azure 防火墙 | 可使用来自前缀的公共 IP 地址作为出站 SNAT。 所有出站虚拟网络流量均转换为 [Azure 防火墙](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 公共 IP。 | 若要将 IP 从前缀关联到防火墙： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. [部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)时，请确保选择之前通过前缀提供的 IP。|
| 应用程序网关 v2 | 对于自动缩放和区域冗余应用程序网关 v2，可以使用来自前缀的公共 IP。 | 若要将 IP 从前缀关联到网关，请执行以下操作： </br> 1.[创建前缀。](manage-public-ip-address-prefix.md) </br> 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) </br> 3. [部署应用程序网关](../application-gateway/quick-create-portal.md#create-an-application-gateway)时，请确保选择之前通过前缀提供的 IP。|

## <a name="constraints"></a>约束

- 不能指定前缀的 IP 地址。 Azure 根据你指定的大小为前缀提供 IP 地址。
- 默认情况下，可以创建最多 16 个 IP 地址的前缀或默认前缀 /28。 查看[网络限制增加请求](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)和 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)以获取详细信息。
- 创建前缀后，无法更改该范围。
- 仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 范围中的地址只能分配到 Azure 资源管理器资源。 无法在经典部署模型中将地址分配给资源。
- 从前缀创建的所有公共 IP 地址都必须与前缀位于同一个 Azure 区域和订阅中。 必须为同一区域和订阅中的资源分配地址。
- 如果前缀中的任何地址被分配到与某个资源关联的公共 IP 地址资源，则无法删除该前缀。 首先应取消关联所有公共 IP 地址资源，这些资源通过前缀分配有 IP 地址。


## <a name="next-steps"></a>后续步骤

- [创建](manage-public-ip-address-prefix.md) 公共 IP 地址前缀
