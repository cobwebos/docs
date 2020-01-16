---
title: Azure VPN 网关：配置诊断日志事件的警报
description: 针对 VPN 网关诊断日志事件配置警报的步骤
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: eb8f0204771b204af740c4ddc8e359499520a012
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045942"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>设置 VPN 网关上诊断日志事件的警报

本文可帮助你使用 Azure Log Analytics 基于 Azure VPN 网关上的诊断日志事件设置警报。 

Azure 中提供了以下日志：

|***名称*** | ***说明*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含网关配置事件、主要更改和维护事件的诊断日志 |
|TunnelDiagnosticLog | 包含隧道状态更改事件。 隧道连接/断开连接事件具有状态更改（如果适用）的汇总原因 |
|RouteDiagnosticLog | 记录在网关上发生的静态路由和 BGP 事件的更改 |
|IKEDiagnosticLog | 在网关上记录 IKE 控制消息和事件 |
|P2SDiagnosticLog | 记录网关上的点到站点控制消息和事件 |

## <a name="setup"></a>设置警报

以下示例步骤将为涉及站点到站点 VPN 隧道的断开连接事件创建警报：


1. 在 Azure 门户中，在 "**所有服务**" 下搜索**Log Analytics** ，然后选择 " **Log Analytics 工作区**"。

   ![转到 Log Analytics 工作区的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. 在**Log Analytics** "页上选择"**创建**"。

   ![带有 "创建" 按钮的 Log Analytics 页面](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "选择")

3. 选择 "**新建**"，并填写详细信息。

   ![创建 Log Analytics 工作区的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "选择")

4. 在 "**监视器** > "**诊断设置**"边栏选项卡上找到 VPN 网关。

   ![用于在诊断设置中查找 VPN 网关的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "选择")

5. 若要启用诊断，请双击该网关，然后选择 "**启用诊断**"。

   ![启用诊断的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "选择")

6. 填写详细信息，并确保选择了 "**发送到 Log Analytics** " 和 " **TunnelDiagnosticLog** "。 选择在步骤3中创建的 "Log Analytics" 工作区。

   ![选中的复选框](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "选择")

   > [!NOTE]
   > 最初显示数据可能需要几个小时。

7. 请参阅虚拟网络网关资源的 "概述"，并从 "**监视**" 选项卡中选择**警报**。然后创建新的警报规则，或编辑现有的警报规则。

   ![用于创建新警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "选择")

   ![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "选择")
8. 选择 "Log Analytics" 工作区和资源。

   ![工作区和资源的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "选择")

9. 在 "**添加条件**" 下选择 "**自定义日志搜索**" 作为信号逻辑。

   ![自定义日志搜索的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "选择")

10. 在“搜索查询”文本框中输入以下查询。 根据需要替换 < > 和 TimeGenerated 中的值。

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    将 "阈值" 设置为0，然后选择 "**完成**"。

    ![输入查询并选择阈值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "选择")

11. 在 "**创建规则**" 页上，选择 "**操作组**" 部分下的 "**新建**"。 填写详细信息，然后选择 **"确定"** 。

    ![新操作组的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "选择")

12. 在 "**创建规则**" 页上，填写**自定义操作**的详细信息，并确保 "**操作组名称**" 部分中显示正确的名称。 选择 "**创建警报规则**" 以创建规则。

    ![用于创建规则的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "选择")

## <a name="next-steps"></a>后续步骤

若要配置针对隧道指标的警报，请参阅[设置有关 VPN 网关指标的警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
