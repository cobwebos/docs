---
title: "使用 Azure PowerShell 管理 Azure DDoS 保护标准 | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 管理 Azure DDoS 保护标准。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8badb3455fa508e4eef56ec7fc1901fbbc8b23d0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure DDoS 保护标准

>[!IMPORTANT]
>Azure DDoS 保护标准（DDoS 保护）当前为预览版。 有限数量的 Azure 资源在选定的区域中支持 DDoS 保护。 在使用受限的预览版期间，需要[针对该服务进行注册](http://aka.ms/ddosprotection)才能为订阅启用 DDoS 保护。 注册后，Azure DDoS 团队会联系你以指导你完成启用过程。 DDoS 保护仅在美国东部、美国西部和美国中部区域可用。 在预览版期间，使用该服务无需付费。

本文演示了如何使用 Azure PowerShell 来启用 DDoS 保护、禁用 DDoS 保护以及如何使用遥测来缓解攻击。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 如果需要安装或升级 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>启用 DDoS 保护

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>创建新的虚拟网络并启用 DDoS 保护

若要创建虚拟网络并启用 DDoS 保护，请运行以下示例：

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

此示例会创建具有两个子网和两个 DNS 服务器的虚拟网络。 在虚拟网络上指定 DNS 服务器的结果是，部署到此虚拟网络的 NIC/VM 会继承这些 DNS 服务器作为默认值。 会为虚拟网络中的所有受保护资源启用 DDoS 保护。

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>在现有的虚拟网络上启用 DDoS 保护

若要在现有的虚拟网络上启用 DDoS 保护，请运行以下示例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>在虚拟网络上禁用 DDoS 保护

若要在虚拟网络上禁用 DDoS 保护，请运行以下示例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>查看虚拟网络的 DDoS 保护状态 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 保护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公用 IP 地址处于风险缓解期间可用。 在攻击缓解前后都看不到遥测。

### <a name="configure-alerts-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 保护指标以在攻击期间发生活跃的风险缓解时发出警报。

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>通过 Azure PowerShell 配置电子邮件警报规则

1. 获取可用订阅的列表。 验证正在使用的正确订阅。 如果没有，则使用 Get-AzureRmSubscription 中的输出将其设置为正确订阅。 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. 若要列出有关资源组的现有规则，可使用以下命令： 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. 若要创建一条规则，首先需要有以下几条重要信息。 

    - 要为其设置警报的资源的资源 ID。
    - 该资源可用的指标定义。 获取资源 ID 的一种方法是使用 Azure 门户。 假定已创建该资源，在 Azure 门户中选中它。 然后在下一页中，选择“设置”部分下的“属性”。 **资源 ID** 是下一页中的字段。 另一种方法是使用 [Azure Resource Explorer](https://resources.azure.com/)（Azure 资源浏览器）。 公共 IP 的示例资源 ID 是：`/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    以下示例设置了一个关于与虚拟网络关联的公共 IP 的警报。 创建警报的指标是**是否遭到 DDoS 攻击**。 这是一个值为 1 或 0 的布尔值。 **1** 表示遭到攻击。 **0** 表示未遭到攻击。 当在最近 5 分钟内遭到攻击时，会创建警报。

    若要在创建警报时创建 webhook 或发送电子邮件，首先要创建电子邮件和/或 webhook。 然后，立即创建后跟 -Actions 标记的规则，如以下示例所示。 无法使用 PowerShell 将 webhook 或电子邮件与已创建的规则相关联。

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. 验证是否已通过查看单个规则正确创建警报。

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

还可以了解有关为创建警报而[配置 webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) 和[逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)的详细信息。

## <a name="configure-logging-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的日志记录

请参阅 [PowerShell 快速入门示例](../monitoring-and-diagnostics/insights-powershell-samples.md)，有助于通过 PowerShell 访问和配置 Azure 诊断日志记录。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
