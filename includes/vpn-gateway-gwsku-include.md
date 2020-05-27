---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74085221"
---
创建虚拟网络网关时，需要指定要使用的网关 SKU。 请根据工作负荷、吞吐量、功能和 SLA 的类型，选择满足需求的 SKU。 有关 Azure 可用性区域中的虚拟网络网关 Sku，请参阅[Azure 可用性区域网关 sku](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)。

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>按隧道、连接和吞吐量列出的网关 SKU

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> 仅资源管理器部署模型支持 VpnGw Sku （VpnGw1、VpnGw1AZ、VpnGw2、VpnGw2AZ、VpnGw3、VpnGw3AZ、VpnGw4、VpnGw4AZ、VpnGw5 和 VpnGw5AZ）。 经典虚拟网络应继续使用老版（旧版）SKU。
>  * 有关如何使用旧版网关 SKU（基本、标准和高性能）的信息，请参阅[使用 VPN 网关 SKU（旧版 SKU）](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。
>  * 如需 ExpressRoute 网关 SKU，请参阅[适用于 ExpressRoute 的虚拟网络网关](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>按功能集列出的网关 SKU

新 VPN 网关 SKU 简化了网关上提供的功能集：

| **SKU**| **功能**|
| ---    | ---         |
|**基本**（* *）   | **基于路由的 VPN**：用于 S2S /连接的 10 个隧道；无适用于 P2S 的 RADIUS 身份验证；无适用于 P2S 的 IKEv2<br>**基于策略的 VPN**：(IKEv1)：1 个 S2S/连接隧道；无 P2S|
| **除基本 SKU 外的所有第 1 代和第 2 代 SKU** | **基于路由的 VPN**：最多30个隧道（*）、P2S、BGP、主动-主动、自定义 IPSEC/IKE 策略、EXPRESSROUTE/VPN 共存 |
|        |             |

(*) 可以对“PolicyBasedTrafficSelectors”进行配置，以便将基于路由的 VPN 网关连接到多个本地基于策略的防火墙设备。 有关详细信息，请参阅[使用 PowerShell 将 VPN 网关连接到多个本地的基于策略的 VPN 设备](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

(\*\*) 基本 SKU 被视为旧版 SKU。 基本 SKU 具有某些功能限制。 使用基本 SKU 的网关无法调整为新网关 SKU 中的一种，必须更改为新的 SKU，这就需要删除并新建 VPN 网关。

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>网关 Sku-生产与开发测试工作负荷

由于 SLA 和功能集的差异，建议使用以下 SKU 比较生产与开发-测试：

| **工作负载**                       | **Sku**               |
| ---                                | ---                    |
| 生产、关键工作负荷**** | 除基本 SKU 外的所有第 1 代和第 2 代 SKU |
| 开发-测试或概念证明****   | 基本 (\*\*)                 |
|                                    |                        |

(\*\*) 基本 SKU 被视为旧版 SKU，并且具有功能限制。 使用基本 SKU 前，请验证所需功能是否受支持。

如果使用老版 SKU（旧版），则推荐使用的生产 SKU 为标准和高性能。 有关老版 SKU 的信息和说明，请参阅[网关 SKU（旧版）](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。
