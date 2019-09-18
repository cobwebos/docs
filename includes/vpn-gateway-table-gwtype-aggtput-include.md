---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919643"
---
|**SKU**   | S2S/VNet 到 VNet<br>隧道  | **P2S<br> SSTP 连接** | **P2S<br> IKEv2/OpenVPN 连接** |  聚合<br>吞吐量基准 | **BGP** | **区域冗余** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**基本** | 最大 10    | 最大 128  | 不支持  | 100 Mbps  | 不支持| 否 |
|VpnGw1 | 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支持 | 否 |
|VpnGw2 | 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支持 | 否 |
|VpnGw3 | 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支持 | 否 |
|**VpnGw1AZ**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支持 | 是 |
|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支持 | 是 |
|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支持 | 是 |

(*) 如果需要 30 个以上 S2S VPN 隧道，请使用[虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* 这些连接限制是独立的。 例如，在 VpnGw1 SKU 上可以有 128 个 SSTP 连接，还可以有 250 个 IKEv2 连接。

* 可在 [定价](https://azure.microsoft.com/pricing/details/vpn-gateway) 页上找到定价信息。

* 可在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 页查看 SLA（服务级别协议）信息。

* 仅在使用资源管理器部署模型的情况下才支持将 VpnGw1、VpnGw2、VpnGw3、VpnGw1AZ、VpnGw2AZ 和 VpnGw3AZ 用于 VPN 网关。

* 基本 SKU 被视为旧版 SKU。 基本 SKU 具有某些功能限制。 使用基本 SKU 的网关无法调整为新网关 SKU 中的一种，必须更改为新的 SKU，这就需要删除并新建 VPN 网关。 使用基本 SKU 前，请验证所需功能是否受支持。

* 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 适用于 VPN 网关的聚合吞吐量基准组合了 S2S 和 P2S。 **如果有大量的 P2S 连接，则可能会对 S2S 连接造成负面影响，因为存在吞吐量限制。** 受 Internet 流量情况和应用程序行为影响，无法保证聚合吞吐量基准。

* 为了帮助我们的客户了解 VpnGw SKU 的相对性能，我们使用市售 iPerf 和 CTSTraffic 工具来衡量性能。 下表列出了使用不同算法的性能测试的结果。 可以看到，对 IPsec 加密和完整性使用 GCMAES256 算法时，可获得最佳性能。 对 IPsec 加密使用 AES256 以及对完整性使用 SHA256 时，可获得平均性能。 对 IPsec 加密使用 DES3 以及对完整性使用 SHA256 可获得最低性能。

|**SKU**   | **使用<br>的算法** | **观察到的<br>吞吐量** | **观察到的<br>每秒数据包数** |
|---       | ---                 | ---            | ---                    |
|VpnGw1 | GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|VpnGw2 | GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|VpnGw3 | GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
