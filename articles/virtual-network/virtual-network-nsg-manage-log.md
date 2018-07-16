---
title: 网络安全组事件和规则计数器 Azure 诊断日志 | Microsoft Docs
description: 了解如何为 Azure 网络安全组启用事件和规则计数器诊断日志。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: 81809660bdda957eb4502e02799b9f7f5538ae51
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114017"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>网络安全组的诊断日志记录

网络安全组 (NSG) 包含的规则可以用来允许或拒绝发往虚拟网络子网和/或网络接口的流量。 为 NSG 启用诊断日志记录时，可以记录以下类别的信息：

* **事件：** 根据 MAC 地址记录的与应用到 VM 的 NSG 规则相对应的条目。 每隔 60 秒收集一次这些规则的状态。
* **规则计数器：** 包含应用每个 NSG 规则以拒绝或允许流量的次数的条目。

诊断日志仅适用于通过 Azure 资源管理器部署模型部署的 NSG。 不能对通过经典部署模型部署的 NSG 启用诊断日志记录。 若要更好地了解这两种模型，请参阅[了解 Azure 部署模型](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

分别为要收集其诊断数据的每个 NSG 启用诊断日志记录。 如果感兴趣的是运行日志或活动日志，请参阅 Azure [活动日志记录](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="enable-logging"></a>启用日志记录

可以使用 [Azure 门户](#azure-portal)、[PowerShell](#powershell) 或 [Azure CLI](#azure-cli) 来启用诊断日志记录。

### <a name="azure-portal"></a>Azure 门户

1. 登录[门户](https://portal.azure.com)。
2. 选择“所有服务”，然后键入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
3. 选择要为其启用日志记录的 NSG。
4. 在“监视”下选择“诊断日志”，然后选择“启用诊断”，如下图所示：
 
    ![启用诊断](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. 在“诊断设置”下输入或选择以下信息，然后选择“保存”：

    | 设置                                                                                     | 值                                                          |
    | ---------                                                                                   |---------                                                       |
    | 名称                                                                                        | 所选名称。  例如：*myNsgDiagnostics*      |
    | “存档到存储帐户”、“流式传输到事件中心”，然后“发送到 Log Analytics” | 可以随意选择多个目标。 若要详细了解每个目标，请参阅[日志目标](#log-destinations)。                                                                                                                                           |
    | 日志                                                                                         | 选择一个或两个日志类别。 若要详细了解为每个类别记录的数据，请参阅[日志类别](#log-categories)。                                                                                                                                             |
6. 查看和分析日志。 有关详细信息，请参阅[查看和分析日志](#view-and-analyze-logs)。

### <a name="powershell"></a>PowerShell

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，需要 *AzureRM* PowerShell 模块 6.1.1 或更高版本。 在计算机上运行 `Get-Module -ListAvailable AzureRM`，找到已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需要运行 `Login-AzureRmAccount`，以使用具有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

若要启用诊断日志记录，需要现有 NSG 的 ID。 如果没有现成的 NSG，则可使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 创建一个。

使用 [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup) 检索要为其启用诊断日志记录的网络安全组。 例如，若要在名为 *myResourceGroup* 的资源组中检索现有的名为 *myNsg* 的 NSG，请输入以下命令：

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

可以将诊断日志写入三种目标类型。 有关详细信息，请参阅[日志目标](#log-destinations)。 例如，在本文中，日志发送到 *Log Analytics* 目标。 使用 [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) 检索现有的 Log Analytics 工作区。 例如，若要在名为 *myWorkspaces* 的资源组中检索名为 *myWorkspace* 的现有工作区，请输入以下命令：

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

如果没有现成的工作区，则可使用 [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) 创建一个。

可以为日志启用两种类别的日志记录。 有关详细信息，请参阅[日志类别](#log-categories)。 使用 [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) 为 NSG 启用诊断日志记录。 以下示例使用 NSG 的 ID 和以前检索的工作区将事件和计数器类别的数据记录到 NSG 的工作区：

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

若要记录其中一个类别的数据而不是两个类别都记录，请将 `-Categories` 选项添加到前一命令，后跟 *NetworkSecurityGroupEvent* 或 *NetworkSecurityGroupRuleCounter*。 若要记录到 Log Analytics 工作区之外的[目标](#log-destinations)，请使用适合 Azure [存储帐户](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或[事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的参数。

查看和分析日志。 有关详细信息，请参阅[查看和分析日志](#view-and-analyze-logs)。

### <a name="azure-cli"></a>Azure CLI

可以在 [Azure Cloud Shell](https://shell.azure.com/bash) 中运行以下命令，或者在计算机上运行 Azure CLI。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 CLI，需要版本 2.0.38 或更高版本。 在计算机上运行 `az --version`，找到已安装的版本。 如果需要进行升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果在本地运行 CLI，则还需要运行 `az login`，以使用具有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

若要启用诊断日志记录，需要现有 NSG 的 ID。 如果没有现成的 NSG，则可使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) 创建一个。

使用 [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) 检索要为其启用诊断日志记录的网络安全组。 例如，若要在名为 *myResourceGroup* 的资源组中检索现有的名为 *myNsg* 的 NSG，请输入以下命令：

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

可以将诊断日志写入三种目标类型。 有关详细信息，请参阅[日志目标](#log-destinations)。 例如，在本文中，日志发送到 *Log Analytics* 目标。 有关详细信息，请参阅[日志类别](#log-categories)。 

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 为 NSG 启用诊断日志记录。 以下示例使用前面检索到的 NSG 的 ID 将事件和计数器类别数据记录到名为 *myWorkspace* 的现有工作空间，该工作空间存在于名为 *myWorkspaces* 的资源组中：

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

如果没有现成的工作区，则可以使用 [Azure 门户](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [PowerShell](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) 创建一个。 可以为日志启用两种类别的日志记录。 

如果只想记录一种类别或另一种类别的数据，请在上一个命令中删除不想记录其数据的类别。 若要记录到 Log Analytics 工作区之外的[目标](#log-destinations)，请使用适合 Azure [存储帐户](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或[事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的参数。

查看和分析日志。 有关详细信息，请参阅[查看和分析日志](#view-and-analyze-logs)。

## <a name="log-destinations"></a>日志目标

诊断数据可以：
- [写入到 Azure 存储帐户](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以便进行审核或手动检查。 可以使用“资源诊断设置”指定保留时间（天）。
- [流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
- [写入到 Azure Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics)。

## <a name="log-categories"></a>日志类别

将为以下日志类别写入 JSON 格式的数据：

### <a name="event"></a>事件

此事件日志记录了根据 MAC 地址将哪些 NSG 规则应用于 VM。 针对每个事件记录以下示例数据：

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>规则计数器

此规则计数器日志记录了应用到资源的每个规则。 每次应用规则时会记录以下示例数据：

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> 通信的源 IP 地址不记录。 但是，可以为 NSG 启用 [NSG 流日志记录](../network-watcher/network-watcher-nsg-flow-logging-portal.md)，以便记录所有规则计数器信息以及启动通信的源 IP 地址。 NSG 流日志数据写入 Azure 存储帐户。 可以使用 Azure 网络观察程序的[流量分析](../network-watcher/traffic-analytics.md)功能来分析数据。

## <a name="view-and-analyze-logs"></a>查看和分析日志

若要了解如何查看诊断日志数据，请参阅 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果将诊断数据发送到：
- **Log Analytics**，则可使用[网络安全组分析](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
)解决方案来获取增强的见解。 此解决方案提供 NSG 规则的可视化效果，此类规则可以根据 MAC 地址允许或拒绝虚拟机中网络接口的流量。
- **Azure 存储帐户**，则将数据写入 PT1H.json 文件。 可以找到：
    - 事件日志，位于以下路径：`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - 规则计数器日志，位于以下路径：`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>后续步骤

- 详细了解[活动日志记录](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（以前称为审核或运行日志）。 默认情况下，对通过任一 Azure 部署模型创建的 NSG 启用活动日志记录。 若要在活动日志中确定在 NSG 上完成的操作，请查找包含以下资源类型的条目：
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- 若要了解如何记录诊断信息，使日志包含每个流的源 IP 地址，请参阅 [NSG 流日志记录](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
