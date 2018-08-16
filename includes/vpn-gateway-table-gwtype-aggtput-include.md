---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cbbb64489acf23c1248e35269e1441dd2a6878e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513976"
---
|**SKU**   | S2S/VNet 到 VNet<br>隧道 | P2S<br>连接 | 聚合<br>吞吐量基准 |
|---       | ---                             | ---                    | ---                         |
|VpnGw1| 最大 30*                         | 最大 128\*\*             | 650 Mbps                    |
|VpnGw2| 最大 30*                         | 最大 128\*\*             | 1 Gbps                      |
|VpnGw3| 最大 30*                         | 最大 128\*\*             | 1.25 Gbps                   |
|**基本** | 最大 10                         | 最大 128               | 100 Mbps                    | 

* (*) 如果需要 30 个以上 S2S VPN 隧道，请使用[虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* (**) 如果需要更多连接，请联系支持人员。 这仅适用于 IKEv2，SSTP 的连接数不能增加。

* 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。

* 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

* 可在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。

* 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2 和 VpnGw3 用于 VPN 网关。