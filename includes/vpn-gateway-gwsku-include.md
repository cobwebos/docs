---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/20/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b1a9d93d9fccf02ba1517e429625150736e539e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159347"
---
创建虚拟网络网关时，需要指定要使用的网关 SKU。 请根据工作负荷、吞吐量、功能和 SLA 的类型，选择满足需求的 SKU。 虚拟网络网关 Azure 可用性区域中的 Sku，请参阅[Azure 可用性区域网关 Sku](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)。

###  <a name="benchmark"></a>按隧道、连接和吞吐量列出的网关 SKU

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>按功能集列出的网关 SKU

新 VPN 网关 SKU 简化了网关上提供的功能集：

| **SKU**| **功能**|
| ---    | ---         |
|**基本** (\*\*)   | **基于路由的 VPN**：用于 S2S /连接的 10 个隧道；无适用于 P2S 的 RADIUS 身份验证；无适用于 P2S 的 IKEv2<br>**基于策略的 VPN**：(IKEv1)：1 个 S2S/连接隧道；无 P2S|
|  VpnGw1、VpnGw2、VpnGw3 | 基于路由的 VPN：最多 30 个隧道 (\*)、P2S、BGP、主动-主动、自定义 IPsec/IKE 策略、ExpressRoute/VPN 共存  |
|        |             |

( * ) 可以对“PolicyBasedTrafficSelectors”进行配置，以便将基于路由的 VPN 网关（VpnGw1、VpnGw2、VpnGw3）连接到多个本地的基于策略的防火墙设备。 有关详细信息，请参阅[使用 PowerShell 将 VPN 网关连接到多个本地的基于策略的 VPN 设备](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

(\*\*) 基本 SKU 被视为旧版 SKU。 基本 SKU 具有某些功能限制。 使用基本 SKU 的网关无法调整为新网关 SKU 中的一种，必须更改为新的 SKU，这就需要删除并新建 VPN 网关。

###  <a name="workloads"></a>网关 SKU - 生产与开发-测试工作负荷

由于 SLA 和功能集的差异，建议使用以下 SKU 比较生产与开发-测试：

| **工作负载**                       | SKU                |
| ---                                | ---                    |
| 生产、关键工作负荷  | VpnGw1、VpnGw2、VpnGw3 |
| 开发-测试或概念证明    | 基本 (\*\*)                 |
|                                    |                        |

(\*\*) 基本 SKU 被视为旧版 SKU，并且具有功能限制。 使用基本 SKU 前，请验证所需功能是否受支持。

如果使用老版 SKU（旧版），则推荐使用的生产 SKU 为标准和高性能。 有关老版 SKU 的信息和说明，请参阅[网关 SKU（旧版）](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。
