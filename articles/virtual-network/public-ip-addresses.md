---
title: Azure 中的公共 IP 地址
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的公共 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 9f3d95d7ae725dba700b0a060ba74552d6b83ad5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84172242"
---
# <a name="public-ip-addresses"></a>公共 IP 地址

公共 IP 地址允许 Internet 资源与 Azure 资源进行入站通信。 公共 IP 地址可让 Azure 资源与 Internet 以及面向公众的 Azure 服务通信。 该地址专用于该资源，直到被您取消分配。 没有分配公共 IP 的资源可以进行出站通信。 Azure 会动态分配专用于资源的可用 IP 地址。 有关 Azure 中的出站连接的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

在 Azure Resource Manager 中，[公共 IP](virtual-network-public-ip-address.md) 地址是具有其自身属性的资源。 可将公共 IP 地址资源关联到的一些资源：

* 虚拟机网络接口
* 面向 Internet 的负载均衡器
* VPN 网关
* 应用程序网关
* Azure 防火墙

## <a name="ip-address-version"></a>IP 地址版本

公共 IP 地址是使用 IPv4 或 IPv6 地址创建的。 

## <a name="sku"></a>SKU

使用以下 SKU 之一创建公共 IP 地址：

>[!IMPORTANT]
> 负载均衡器和公共 IP 资源需要匹配的 Sku。 不能混合使用基本 SKU 资源和标准 SKU 资源。 无法将独立的虚拟机、可用性集资源中的虚拟机或虚拟机规模集资源同时附加到两个 SKU。  新的设计应当考虑使用标准 SKU 资源。  有关详细信息，请查看[标准负载均衡器](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="standard"></a>标准

标准 SKU 公共 IP 地址：

- 始终使用静态分配方法。
- 具有可调整的入站发起流空闲超时，范围为 4-30 分钟，默认值为 4 分钟，出站发起流的空闲超时固定为 4 分钟。
- 默认情况下，安全并关闭入站流量。 允许列出包含[网络安全组](security-overview.md#network-security-groups)的入站流量。
- 分配到网络接口、标准公共负载均衡器或应用程序网关。 有关标准负载均衡器的详细信息，请参阅[Azure 标准负载均衡器](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 可以是区域冗余或区域性（可在特定的可用性区域中创建区域和保证）。 若要详细了解可用性区域，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
 
> [!NOTE]
> 在创建并关联[网络安全组](security-overview.md#network-security-groups)且显式允许所需入站流量之前，到标准 SKU 资源的入站通信将会失败。

> [!NOTE]
> 使用[实例元数据服务 IMDS](../virtual-machines/windows/instance-metadata-service.md) 时，只有具有基本 SKU 的公共 IP 地址可用。 不支持标准 SKU。

### <a name="basic"></a>基本

推出 SKU 之前创建的所有公共 IP 地址为基本 SKU 公共 IP 地址。 

引入 Sku 后，指定想要公共 IP 地址的 SKU。 

基本 SKU 地址：

- 使用静态或动态分配方法分配。
- 具有可调整的入站发起流空闲超时，范围为 4-30 分钟，默认值为 4 分钟，出站发起流的空闲超时固定为 4 分钟。
- 默认情况下处于打开状态。  建议使用网络安全组来对入站或出站流量进行限制，但这是可选的。
- 分配给可分配公共 IP 地址的任何 Azure 资源，例如：
    * 网络接口
    * VPN 网关
    * 应用程序网关
    * 公共负载均衡器
- 不支持可用性区域方案。 为可用性区域方案使用标准 SKU 公共 IP。 若要详细了解可用性区域，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="allocation-method"></a>分配方法

基本和标准公共 Ip 支持**静态**分配。  创建资源时，会为资源分配一个 IP 地址。 删除资源时，将释放该 IP 地址。

基本 SKU 公共 IP 地址支持**动态**分配。 动态是默认的分配方法。 选择 "动态" 时，**不**会在创建时将 IP 地址提供给资源。

将公共 IP 地址资源与关联时，会分配 IP：

* 虚拟机 
* 第一个虚拟机与负载均衡器的后端池相关联。

停止（或删除）该资源时，就会释放该 IP 地址。  

例如，从名为**资源 a**的资源中释放公共 IP 资源。如果重新分配公共 IP 资源，**资源 A**将在启动时接收其他 ip。

当分配方法从**静态**更改为**动态**时，将释放该 IP 地址。 若要确保关联资源的 IP 地址保持不变，请将分配方法显式设置为**静态**。 静态 IP 地址是立即分配的。

> [!NOTE]
> 即使将分配方法设置为“静态”，也无法通过指定方式将实际 IP 地址分配到公共 IP 地址资源。**** Azure 会从创建资源时所在的 Azure 位置的可用 IP 地址池中分配 IP 地址。
>

以下情况通常使用静态公共 IP 地址：

* 必须更新防火墙规则才能与 Azure 资源通信。
* 对 DNS 名称进行解析时，如果更改了 IP 地址，则需更新 A 记录。
* Azure 资源可与使用基于 IP 地址的安全模型的其他应用或服务通信。
* 使用链接到 IP 地址的 TLS/SSL 证书。

> [!NOTE]
> Azure 会从每个 Azure 云中每个区域的唯一地址范围中分配公共 IP 地址。 对于 Azure [公有](https://www.microsoft.com/download/details.aspx?id=56519)云、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)云、[中国](https://www.microsoft.com/download/details.aspx?id=57062)云和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云，可以下载范围（前缀）的列表。
>

## <a name="dns-hostname-resolution"></a>DNS 主机名解析

选择该选项以指定公共 IP 资源的 DNS 域名标签。 

此选择将为**domainnamelabel**创建映射。cloudapp.azure.com Azure 托管 DNS 中的公共**IP。** 

例如，使用以下内容创建公共 IP：

* **contoso**作为**domainnamelabel**
* **美国西部**Azure**位置**

完全限定的域名（FQDN） **contoso.westus.cloudapp.azure.com**解析为资源的公共 IP 地址。

> [!IMPORTANT]
> 所创建的每个域名标签在其 Azure 位置必须是唯一的。  
>

## <a name="dns-recommendations"></a>DNS 建议

如果需要移动区域，则无法迁移公共 IP 的 FQDN。 使用 FQDN 创建指向公共 IP 地址的自定义 CNAME 记录。 

如果需要移动到不同的公共 IP，请更新 CNAME 记录，而不是更新 FQDN。

可以将 [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 或外部 DNS 提供程序用于 DNS 记录。 

## <a name="virtual-machines"></a>虚拟机

将公共 IP 地址分配到其**网络接口**可将其与 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 

对于公共 IP 地址，请选择 "**动态**" 或 "**静态**"。 详细了解如何[将 IP 地址分配到网络接口](virtual-network-network-interface-addresses.md)。

## <a name="internet-facing-load-balancers"></a>面向 Internet 的负载均衡器

可以将任一[SKU](#sku)的公共 IP 地址与[Azure 负载均衡器](../load-balancer/load-balancer-overview.md)相关联，方法是将其分配给负载均衡器**前端**配置。 公共 IP 充当负载平衡的 IP。 

可以向负载均衡器前端分配动态或静态公共 IP 地址。 可以向负载均衡器前端分配多个公共 IP 地址。 此配置启用[多 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方案，如包含基于 TLS 的网站的多租户环境。 

有关 Azure 负载均衡器 SKU 的详细信息，请参阅 [Azure 负载均衡器标准 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="vpn-gateways"></a>VPN 网关

[AZURE VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)将 azure 虚拟网络连接到：

* Azure 虚拟网络
* 本地网络。 

公共 IP 地址分配到 VPN 网关，以启用与远程网络的通信。 只能向 VPN 网关分配”动态”基本的公共 IP 地址。**

## <a name="application-gateways"></a>应用程序网关

将公共 IP 地址分配给网关的**前端**配置可以将其与 Azure [应用程序网关](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)相关联。 

* 将**动态**基本公共 IP 分配给应用程序网关 V1 前端配置。 
* 将**静态**标准 SKU 地址分配到 V2 前端配置。

## <a name="at-a-glance"></a>概览

下表显示了一个属性，可通过该属性将公共 IP 关联到顶级资源和可能的分配方法。

| 顶级资源 | IP 地址关联 | 动态 | 静态 |
| --- | --- | --- | --- |
| 虚拟机 |Linux |是 |是 |
| 面向 Internet 的负载均衡器 |前端配置 |是 |是 |
| VPN 网关 |网关 IP 配置 |是 |否 |
| 应用程序网关 |前端配置 |是（仅限 V1） |是（仅限 V2） |

## <a name="limits"></a>限制

IP 寻址限制在 Azure 中的网络的完整[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)集中列出。 

这些限制根据区域和订阅设置。 [与支持人员联系](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，根据业务需求将默认限制提高到最大限制。

## <a name="pricing"></a>定价

公共 IP 地址可能会产生少许费用。 有关 Azure 中 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

## <a name="next-steps"></a>后续步骤
* 了解[Azure 中的专用 IP 地址](private-ip-addresses.md)
* [使用 Azure 门户通过静态公共 IP 部署 VM](virtual-network-deploy-static-pip-arm-portal.md)

