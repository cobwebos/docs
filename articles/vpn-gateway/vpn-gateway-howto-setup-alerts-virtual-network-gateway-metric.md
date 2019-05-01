---
title: 如何设置 Azure VPN 网关指标相关警报
description: VPN 网关指标配置警报的步骤
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769461"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>设置 VPN 网关指标相关警报

本文可帮助你设置 VPN 网关度量值的警报。 Azure monitor 提供的 Azure 资源设置警报的能力。 对于"VPN"类型的虚拟网络网关，可以设置警报。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 平均网关上的所有站点到站点连接的组合的带宽的使用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 平均网关上的所有点到站点连接的组合的带宽的使用率。    |
|**P2SConnectionCount**| Count  | 1 分钟  | 在网关上的计数的 P2S 连接。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关创建的隧道的平均带宽利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关创建的隧道的传出流量。   |
|**TunnelEgressPackets** | Count | 5 分钟 | 在网关创建的隧道上的传出数据包计数。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分钟 | 在通过 TS 不匹配导致的隧道上丢弃的传出数据包计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关创建的隧道的传入流量。   |
|**TunnelIngressPackets** | Count | 5 分钟 | 在网关创建的隧道上的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分钟 | 在通过 TS 不匹配导致的隧道上丢弃的传入数据包计数。 |


## <a name="setup"></a>设置 Azure Monitor 警报基于指标使用门户

下面的示例步骤将创建的网关有关的警报： <br>

**指标：** 隧道平均带宽 <br>
**条件：** 带宽 > 10 个字节/秒 <br>
**窗口：** 5 分钟 <br>
**警报操作：** 电子邮件 <br>



1. 导航到虚拟网络网关资源并从监视选项卡，选择"警报"，然后创建新的警报规则或编辑现有警报规则。

![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "创建")

2. 选择 VPN 网关的资源。

![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "选择")

3. 选择要配置警报的指标![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "选择")
4. 配置信号逻辑。 有三个组件与信号逻辑：

    a. 尺寸：如果该度量值维度，以便警报仅在计算该维度的数据，可以选择特定维度值。 这些是可选的。<br>
    b. 条件：要评估的指标值的操作。<br>
    c. 时间：指定的指标数据的粒度和时间上评估警报的时间。<br>

![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "选择")

5. 若要查看配置的规则，请单击"管理警报规则"![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "选择")

## <a name="next-steps"></a>后续步骤

若要配置隧道诊断日志的警报，请参阅[如何对 VPN 网关诊断日志设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
