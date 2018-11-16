---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b698dd03473dd3cb708c47c6554869eebba48bf9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597579"
---
|**SKU**   | S2S/VNet 到 VNet<br>隧道 | **P2S<br> SSTP 连接** | **P2S<br> IKEv2 连接** | 聚合<br>吞吐量基准 | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**基本** | 最大 10    | 最大 128  | 不支持  | 100 Mbps  | 不支持|
|VpnGw1| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支持 |
|VpnGw2| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支持 |
|VpnGw3| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支持 |


(*) 如果需要 30 个以上 S2S VPN 隧道，请使用[虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* 适用于 VPN 网关的聚合吞吐量基准组合了 S2S 和 P2S。 **如果有大量的 P2S 连接，则可能会对 S2S 连接造成负面影响，因为存在吞吐量限制。** 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。

* 这些连接限制是独立的。 例如，在 VpnGw1 SKU 上可以有 128 个 SSTP 连接，还可以有 250 个 IKEv2 连接。

* 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

* 可在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。

* 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2 和 VpnGw3 用于 VPN 网关。
