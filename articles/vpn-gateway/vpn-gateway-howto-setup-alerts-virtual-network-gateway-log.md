---
title: 如何设置 Azure VPN 网关诊断日志事件的警报
description: 有关 VPN 网关诊断日志事件配置警报的步骤
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769731"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>有关 VPN 网关诊断日志事件的警报设置

本文可帮助你设置基于 VPN 网关诊断日志事件的警报。


## <a name="setup"></a>根据诊断日志事件使用门户设置 Azure 监视器警报

下面的示例步骤将创建站点到站点 VPN 隧道断开连接事件的警报



1. 在所有服务下搜索"Log Analytics"并选择"Log Analytics 工作区"![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "创建")

2. 在 Log Analytics 页上单击"创建"。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "选择")

3. 检查工作区中"新建"并填写详细信息。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "选择")

4. 查找 VPN 网关在"监视器">"诊断设置"边栏选项卡![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "选择")

5. 若要启用诊断，双击在网关，然后单击"打开诊断"![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "选择")

6. 填写详细信息，并确保"发送到 Log Analytics"和"TunnelDiagnosticLog"进行检查。 选择在步骤 3 中创建 Log Analytics 工作区。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "选择")

7. 导航到虚拟网络网关资源概述和从监视选项卡，选择"警报"，然后创建新的警报规则或编辑现有警报规则。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "选择")

8. 选择 Log Analytics 工作区和资源。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "选择")

9. 选择"自定义日志搜索"作为信号逻辑"添加条件"下的![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "选择")

10. 替换为相应的 <> 中的值在"搜索查询"文本框中输入以下查询。

    AzureDiagnostics |其中类别 = ="TunnelDiagnosticLog"和 ResourceId = = toupper ("<RESOURCEID OF GATEWAY>") 和 TimeGenerated > ago(5m) 和 remoteIP_s = ="<REMOTE IP OF TUNNEL>"和 status_s = ="已断开连接"

    设置阈值的值为 0，然后单击"完成"

    ![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "选择")

11. 在"创建规则"页上，单击"新建"下的操作组部分。 填写详细信息，并单击确定

![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "选择")

12. 在"创建规则"页上，填写的"自定义操作"的详细信息，并确保正确的操作组名称将显示在"操作组名称"部分。 单击"创建警报规则"创建规则。
![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "选择")

## <a name="next-steps"></a>后续步骤

若要在隧道指标配置警报，请参阅[如何对 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
