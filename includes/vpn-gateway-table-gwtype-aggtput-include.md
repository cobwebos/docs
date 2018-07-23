---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138034"
---
|**SKU**   | S2S/VNet 到 VNet<br>隧道 | P2S<br>连接 | 聚合<br>吞吐量基准 |
|---       | ---                             | ---                    | ---                         |
|VpnGw1| 每个  30*                         | 每个  128**             | 650 Mbps                    |
|VpnGw2| 每个  30*                         | 每个  128**             | 1 Gbps                      |
|VpnGw3| 每个  30*                         | 每个  128**             | 1.25 Gbps                   |
|**基本** | 每个  10                         | 每个  128               | 100 Mbps                    | 

* (*) 如果需要 30 个以上 S2S VPN 隧道，请使用[虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* (**) 如果需要更多连接，请联系支持人员

* 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。

* 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

* 可在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。

* 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2 和 VpnGw3 用于 VPN 网关。