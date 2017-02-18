---
title: "监视 NSG 的操作、事件和计数器 | Microsoft 文档"
description: "了解如何为 NSG 启用计数器、事件和操作日志记录"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 8d370f98a4ef2501afc692af8a19a0625f54b678
ms.openlocfilehash: a087b74470a8aa0f70b56d74cd97fe0935d35bcd


---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>网络安全组 (NSG) 的日志分析

可以为 NSG 启用以下诊断日志类别：

* **事件：**包含根据 MAC 地址向 VM 和实例角色应用的 NSG 规则条目。 每隔 60 秒收集一次这些规则的状态。
* **规则计数器：**包含应用每个 NSG 规则以拒绝或允许流量的次数的条目。

> [!NOTE]
> 诊断日志仅适用于通过 Azure Resource Manager 部署模型部署的 NSG。 不能对通过经典部署模型部署的 NSG 启用诊断日志记录。 若要更好地了解这两种模型，可参考[了解 Azure 部署模型](../resource-manager-deployment-model.md)一文。

默认情况下，对通过任一 Azure 部署模型创建的 NSG 启用活动日志记录（以前称为审核日志或操作日志）。 若要在活动日志中确定对 NSG 的哪些操作已完成，请查找包含以下资源类型的条目：Microsoft.ClassicNetwork/networkSecurityGroups、Microsoft.ClassicNetwork/networkSecurityGroups/securityRules、Microsoft.Network/networkSecurityGroups 和 Microsoft.Network/networkSecurityGroups/securityRules。 阅读 [Azure 活动日志概述 ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)一文可了解有关活动日志的详细信息。 

## <a name="enable-diagnostic-logging"></a>启用诊断日志记录

必须为要收集其数据的*每个* NSG 启用诊断日志记录。 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)一文介绍了可以将诊断日志发送到的位置。 如果还没有 NSG，请完成[创建网络安全组](virtual-networks-create-nsg-arm-pportal.md)一文中的步骤以创建一个。 可以使用以下任一方法启用 NSG 诊断日志记录：

### <a name="azure-portal"></a>Azure 门户

若要使用门户启用日志记录，请登录到[门户](https://portal.azure.com)。 单击“更多服务”，然后键入“网络安全组”。 选择要为其启用日志记录的 NSG。 按照[在门户中启用诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-in-the-portal)一文中非计算资源的说明进行操作。 选择 **NetworkSecurityGroupEvent**、**NetworkSecurityGroupRuleCounter** 或这两个日志类别。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 启用日志记录，请按照[通过 PowerShell 启用诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-powershell)一文中的说明进行操作。 在输入该文中的命令之前，评估以下信息：

- 可以根据需要替换以下 [text]，然后输入命令 `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]` 来确定要用于 `-ResourceId` 的值。 命令的 ID 输出看起来类似于 */subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*。
- 如果只想收集日志类别的数据，请在本文中命令的末尾添加 `-Categories [category]`，其中 category 是 *NetworkSecurityGroupEvent* 或 *NetworkSecurityGroupRuleCounter*。 如果未使用 `-Categories` 参数，则将对这两个日志类别启用数据收集。

### <a name="azure-command-line-interface-cli"></a>Azure 命令行接口 (CLI)

若要使用 CLI 启用日志记录，请按照[通过 CLI 启用诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-cli)一文中的说明进行操作。 在输入该文中的命令之前，评估以下信息：

- 可以根据需要替换以下 [text]，然后输入命令 `azure network nsg show [resource-group-name] [nsg-name]` 来确定要用于 `-ResourceId` 的值。 命令的 ID 输出看起来类似于 */subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]*。
- 如果只想收集日志类别的数据，请在本文中命令的末尾添加 `-Categories [category]`，其中 category 是 *NetworkSecurityGroupEvent* 或 *NetworkSecurityGroupRuleCounter*。 如果未使用 `-Categories` 参数，则将对这两个日志类别启用数据收集。

## <a name="logged-data"></a>已记录的数据

将对这两种日志写入 JSON 格式的数据。 以下部分中列出了针对每种日志类型写入的特定数据：

### <a name="event-log"></a>事件日志
此日志包含有关根据 MAC 地址将哪些 NSG 规则应用于 VM 和云服务角色实例的信息。 针对每个事件记录以下示例数据：

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>规则计数器日志

此日志包含有关应用于资源的每个规则的信息。 每次应用规则时将记录以下示例数据：

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>查看和分析日志

若要了解如何查看活动日志数据，请阅读 [Azure 活动日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)一文。 若要了解如何查看诊断日志数据，请阅读 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)一文。 如果将诊断数据发送到 Log Analytics，可以使用 [Azure 网络安全组分析](../log-analytics/log-analytics-azure-networking-analytics.md)（预览版）管理解决方案获得增强的见解。 



<!--HONumber=Feb17_HO1-->


