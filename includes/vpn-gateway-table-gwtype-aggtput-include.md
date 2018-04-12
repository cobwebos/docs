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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
|**SKU**   | S2S/VNet 到 VNet<br>隧道 | P2S<br>连接 | 聚合<br>吞吐量基准 |
|---       | ---                             | ---                    | ---                         |
|VpnGw1| 每个  30                         | 每个  128               | 650 Mbps                    |
|VpnGw2| 每个  30                         | 每个  128               | 1 Gbps                      |
|VpnGw3| 每个  30                         | 每个  128               | 1.25 Gbps                   |
|**基本** | 每个  10                         | 每个  128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。

- 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

- 可在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。

- 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2 和 VpnGw3 用于 VPN 网关。