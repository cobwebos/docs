---
title: 设置基于 Azure VPN 网关指标的警报
description: 基于 VPN 网关指标配置警报的步骤
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605231"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>基于 VPN 网关指标设置警报

本文介绍如何基于 Azure VPN 网关指标设置警报。 Azure Monitor 提供为 Azure 资源设置警报的功能。 可以为“VPN”类型的虚拟网关设置警报。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 网关上所有站点到站点连接的组合带宽平均利用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 网关上所有点到站点连接的组合带宽平均利用率。    |
|**P2SConnectionCount**| Count  | 1 分钟  | 网关上点到站点连接的计数。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关上创建的隧道的带宽平均利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传出流量。   |
|**TunnelEgressPackets** | Count | 5 分钟 | 在网关上创建的隧道中的传出数据包的计数。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传出数据包的计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传入流量。   |
|**TunnelIngressPackets** | Count | 5 分钟 | 在网关上创建的隧道中的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传入数据包的计数。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 门户基于指标设置 Azure Monitor 警报

以下示例步骤将在网关上针对以下项创建一个警报：

- **指标：** 隧道平均带宽
- **条件：** 带宽> 10 字节/秒
- **窗口：** 5 分钟
- **警报操作：** 电子邮件



1. 转到虚拟网络网关资源并从 **"监视"** 选项卡中选择 **"警报**"。然后创建新的警报规则或编辑现有的警报规则。

   ![用于创建警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "创建")

2. 选择 VPN 网关作为资源。

   ![资源列表中的“选择”按钮和 VPN 网关](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. 选择要为警报配置的指标。

   ![指标列表中的所选指标](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. 配置信号逻辑。 它有三个组件：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **维度**：如果指标具有维度，则可以选择特定的维度值，以便警报仅评估该维度的数据。 这些是可选的。

    b.保留“数据库类型”设置，即设置为“共享”。 **条件**：这是计算指标值的操作。

    c. **时间**：指定指标数据的粒度以及评估警报的时间段。

   ![信号逻辑配置详情](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 若要查看配置的规则，请选择“管理警报规则”。****

   ![用于管理警报规则的按钮](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>后续步骤

若要基于隧道诊断日志配置警报，请参阅[基于 VPN 网关诊断日志设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
