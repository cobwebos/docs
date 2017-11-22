---
title: "使用 Azure PowerShell 管理 Azure DDoS 保护标准 | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 管理 Azure DDoS 保护标准。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure DDoS 保护标准

了解如何启用和禁用分布式拒绝服务 (DDoS) 保护，并使用标准 Azure DDoS 保护通过遥测来缓解 DDoS 攻击。 标准 DDoS 保护可保护分配有 Azure [公共 IP 地址](virtual-network-public-ip-address.md)的 Azure 资源，如虚拟机、负载均衡器和应用程序网关等。 若要了解有关标准 DDoS 保护及其功能的详细信息，请参阅[标准 DDoS 保护概述](ddos-protection-overview.md)。 

>[!IMPORTANT]
>Azure DDoS 保护标准（DDoS 保护）当前为预览版。 有限数量的 Azure 资源支持 DDoS 保护，DDoS 保护仅在选定的几个区域中可用。 有关可用区域的列表，请参阅[标准 DDoS 保护概述](ddos-protection-overview.md)。 在使用受限的预览版期间，需要[针对该服务进行注册](http://aka.ms/ddosprotection)才能为订阅启用 DDoS 保护。 注册后，Azure DDoS 团队会联系你，指导你完成启用过程。


## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 如果需要安装或升级 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>启用标准 DDoS 保护 - 新建虚拟网络

若要创建虚拟网络并启用 DDoS 保护，请运行以下示例：

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

此示例会创建具有两个子网和两个 DNS 服务器的虚拟网络。 在虚拟网络上指定 DNS 服务器的结果是，部署到此虚拟网络的 NIC/VM 会继承这些 DNS 服务器作为默认值。 会为虚拟网络中的所有受保护资源启用 DDoS 保护。

> [!WARNING]
> 选择区域时，从[标准 Azure DDoS 保护概述](ddos-protection-overview.md)中的列表中选择支持的区域。

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>在现有的虚拟网络上启用 DDoS 保护

若要在现有的虚拟网络上启用 DDoS 保护，请运行以下示例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> 虚拟网络必须位于支持的区域中。 有关支持的区域的列表，请参阅[标准 Azure DDoS 保护概述](ddos-protection-overview.md)。

## <a name="disable-ddos-protection-on-a-virtual-network"></a>在虚拟网络上禁用 DDoS 保护

若要在虚拟网络上禁用 DDoS 保护，请运行以下示例：

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>查看虚拟网络的 DDoS 保护状态 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 保护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公用 IP 地址处于风险缓解期间可用。 在攻击缓解前后都看不到遥测。

### <a name="configure-alerts-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 保护指标以在攻击期间发生活跃的风险缓解时发出警报。

#### <a name="configure-email-alert-rules-via-azure"></a>通过 Azure 配置电子邮件警报规则

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

3. 若要创建规则，需要先提供以下信息： 

    - 要为其设置警报的资源的资源 ID。
    - 该资源可用的指标定义。 获取资源 ID 的一种方法是使用 Azure 门户。 假定已创建该资源，在 Azure 门户中选中它。 然后在下一页中，选择“设置”部分下的“属性”。 **资源 ID** 是下一页中的字段。 另一种方法是使用 [Azure Resource Explorer](https://resources.azure.com/)（Azure 资源浏览器）。 公共 IP 的示例资源 ID 是：`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    以下示例为虚拟网络中关联到资源的公共 IP 地址创建警报。 创建警报的指标是**是否遭到 DDoS 攻击**。 这是一个值为 1 或 0 的布尔值。 **1** 表示遭到攻击。 **0** 表示未遭到攻击。 如果在过去 5 分钟内发起了一项攻击，则会创建警报。

    若要在创建警报时创建 Webhook 或发送电子邮件，首先要创建电子邮件和/或 Webhook。 创建电子邮件或 Webhook 以后，立即使用 `-Actions` 标记创建规则，如以下示例所示。 无法使用 PowerShell 将 Webhook 或电子邮件与现有的规则相关联。

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. 通过查看规则，验证是否已正确创建警报：

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

还可以了解有关为创建警报而[配置 webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和[逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)的详细信息。

## <a name="configure-logging-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的日志记录

请参阅 [PowerShell 快速入门示例](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，有助于通过 PowerShell 访问和配置 Azure 诊断日志记录。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)