---
title: include 文件
description: include 文件
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485474"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我们最近将所有默认限制提高到了最大限制。 If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| 资源 | Default/maximum limit | 
| --- | --- |
| 虚拟网络 |1,000 |
| 每个虚拟网络的子网数 |3,000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |500,000 |
| Network interface cards |65,536 |
| 网络安全组 |5,000 |
| 每个 NSG 的 NSG 规则数 |1,000 |
| 为安全组中的源或目标指定的 IP 地址和范围数 |4,000 |
| 应用程序安全组 |3,000 |
| 每个 IP 配置和每个 NIC 的应用程序安全组数 |20 |
| 每个应用程序安全组的 IP 配置数 |4,000 |
| 可在网络安全组的所有安全规则中指定的应用程序安全组数 |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| 虚拟网络 TAP |100 |
| 每个虚拟网络 TAP 的网络接口 TAP 配置 |100 |

#### <a name="publicip-address"></a>公共 IP 地址限制
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 公共 IP 地址数 - 动态 | 1,000 for Basic. |联系支持人员。 |
| 公共 IP 地址数 - 静态 | 1,000 for Basic. |联系支持人员。 |
| 公共 IP 地址数 - 静态 | 1,000 for Standard.|联系支持人员。 |
| Public IP prefix length | /28 | 联系支持人员。 |

#### <a name="load-balancer"></a>Load balancer limits
以下限制仅适用于每个订阅按区域通过 Azure 资源管理器管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| 资源 | Default/maximum limit |
| --- | --- |
| 负载均衡器 | 1,000 | 
| 每个资源的规则数，基本 | 250 |
| 每个资源的规则数，标准 | 1,500 | 
| 每个 IP 配置的规则数 | 299 |
| 每个 NIC 的规则数 | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 虚拟网络 |100 |100 |
| 本地网络站点 |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4,096 |4,096 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| 网络安全组 (NSG) |200 |200 |
| 每个 NSG 的 NSG 规则数 |1,000 |1,000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| 公共 IP 地址 (动态) |500 |500 |
| 保留的公共 IP 地址 |500 |500 |
| 每个部署的公共 VIP |5 |联系支持人员 |
| Private VIP (internal load balancing) per deployment |第 |第 |
| Endpoint access control lists (ACLs) |50 |50 |
