---
title: 设置基于 Azure VPN 网关指标的警报
description: 了解如何设置 Azure VPN 网关指标的警报。 使用 Azure Monitor 为 VPN 类型的虚拟网络网关设置警报。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 3819f653cba80854a65a08f92a10f82057abb0fd
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923061"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>基于 VPN 网关指标设置警报

本文介绍如何基于 Azure VPN 网关指标设置警报。 Azure Monitor 提供为 Azure 资源设置警报的功能。 可以为“VPN”类型的虚拟网关设置警报。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 网关上所有站点到站点连接的组合带宽平均利用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 网关上所有点到站点连接的组合带宽平均利用率。    |
|**P2SConnectionCount**| 计数  | 1 分钟  | 网关上点到站点连接的计数。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关上创建的隧道的带宽平均利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传出流量。   |
|**TunnelEgressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传出数据包的计数。   |
|**TunnelEgressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传出数据包的计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传入流量。   |
|**TunnelIngressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传入数据包的计数。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 门户基于指标设置 Azure Monitor 警报

以下示例步骤将在网关上针对以下项创建一个警报：

- **度量值：** TunnelAverageBandwidth
- **条件：** 每秒带宽 > 10 字节
- **窗口：** 5 分钟
- **警报操作：** 电子邮件



1. 请在 "**监视**" 选项卡中，选择 "虚拟网络网关" 资源并选择 "**警报**"。然后创建新的警报规则，或编辑现有的警报规则。

   ![用于创建警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "创建")

2. 选择 VPN 网关作为资源。

   ![资源列表中的“选择”按钮和 VPN 网关](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "选择")

3. 选择要为警报配置的指标。

   ![指标列表中的所选指标](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. 配置信号逻辑。 它有三个组件：

    a. **维度**：如果度量值具有维度，则可以选择特定维度值以便警报仅评估该维度的数据。 这些是可选的。

    b. **条件**：此操作用于计算指标值。

    c. **Time**：指定指标数据的粒度和用于评估警报的时间段。

   ![信号逻辑配置详情](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 若要查看配置的规则，请选择“管理警报规则”。****

   ![用于管理警报规则的按钮](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>后续步骤

若要配置对隧道资源日志的警报，请参阅[设置 VPN 网关资源日志的警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
