---
title: Azure VPN 网关：配置诊断资源日志事件的警报
description: 了解如何使用 Azure Monitor Log Analytics 和 Azure 中可用的资源日志，基于 Azure VPN 网关上的资源日志事件设置警报。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: d79bdc669ab2c3785b1cde569cfce3021e4d924f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923180"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>针对 VPN 网关中的资源日志事件设置警报

本文介绍如何使用 Azure Monitor Log Analytics 根据 Azure VPN 网关上的资源日志事件设置警报。 

Azure 中提供以下资源日志：

|***名称*** | ***说明*** |
|---        | ---               |
|GatewayDiagnosticLog | 包含网关配置事件、主要更改和维护事件的资源日志 |
|TunnelDiagnosticLog | 包含隧道状态更改事件。 在适用的情况下，隧道连接/断开连接事件将汇总有关状态更改的原因 |
|RouteDiagnosticLog | 记录网关上的静态路由和 BGP 事件发生的更改 |
|IKEDiagnosticLog | 记录网关上的 IKE 控制消息和事件 |
|P2SDiagnosticLog | 记录网关上的点到站点控制消息和事件。 仅为 IKEv2 连接提供连接源信息 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>在 Azure 门户中设置警报

以下示例步骤创建了涉及站点到站点 VPN 隧道的断开连接事件的警报：


1. 在 Azure 门户中的“所有服务”下搜索 **Log Analytics**，然后选择“Log Analytics 工作区”。********

   ![转到 Log Analytics 工作区的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "创建")

2. 在“Log Analytics”页上选择“创建”。********

   ![带有 "创建" 按钮的 Log Analytics 页面](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. 选择“新建”并填写详细信息。****

   ![创建 Log Analytics 工作区的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. 在 "**监视**  >  **诊断设置**" 边栏选项卡中找到 VPN 网关。

   ![用于在诊断设置中查找 VPN 网关的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Select")

5. 若要启用诊断，请双击该网关，然后选择“启用诊断”。****

   ![启用诊断的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Select")

6. 填写详细信息，并确保选中“发送到 Log Analytics”和“TunnelDiagnosticLog”。******** 选择在步骤 3 中创建的 Log Analytics 工作区。

   ![选中的复选框](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

   > [!NOTE]
   > 最初显示数据可能需要几个小时。

7. 请参阅虚拟网络网关资源的 "概述"，并从 "**监视**" 选项卡中选择**警报**。然后创建新的警报规则，或编辑现有的警报规则。

   ![用于创建新警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

   ![点到站点](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. 选择 Log Analytics 工作区和资源。

   ![工作区和资源的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. 在“添加条件”下，选择“自定义日志搜索”作为信号逻辑。********

   ![自定义日志搜索的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Select")

10. 在 "**搜索查询**" 文本框中输入以下查询。 根据需要替换 <> 和 TimeGenerated 中的值。

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

    将阈值设置为 0，然后选择“完成”。****

    ![输入查询并选择阈值](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. 在“创建规则”页上的“操作组”部分下，选择“新建”。************ 填写详细信息，然后选择“确定”。****

    ![新操作组的详细信息](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. 在“创建规则”页上，填写“自定义操作”的详细信息，并确保“操作组名称”部分中显示的名称正确。************ 选择“创建警报规则”以创建规则。****

    ![用于创建规则的选择](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>使用 PowerShell 设置警报

以下示例步骤为涉及站点到站点 VPN 隧道的断开连接事件创建警报。

1. 创建 Log Analytics 工作区：

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. 为 VPN 网关启用诊断：

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. 创建操作组。

   此代码将创建一个在触发警报时发送电子邮件通知的操作组：

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. 基于自定义日志搜索创建警报规则：

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>后续步骤

若要针对隧道指标配置警报，请参阅[针对 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。
