---
title: "使用 Azure 网络观察程序安全组视图自动进行 NSG 审核 | Microsoft 文档"
description: "本页说明如何配置网络安全组的审核"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: d60b1d44844c449e0f66dc0107a25531569d097b
ms.openlocfilehash: a91da330e677c85f16f6f4e506613576b6507d7c
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---

# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>使用 Azure 网络观察程序安全组视图自动进行 NSG 审核

客户经常会遇到验证基础结构安全局势的难题。 对于 Azure 中的 VM，也存在类似的难题。 必须应用一个基于网络安全组 (NSG) 规则的类似安全配置文件。 现在，使用安全组视图可以获取应用到 NSG 中 VM 的规则列表。 可以定义一个黄金 NSG 安全配置文件，然后每周启动安全组视图，将输出与该黄金配置文件进行比较并创建报告。 这样，便可以轻松识别不符合指定的安全配置文件的所有 VM。

如果不熟悉网络安全组，请访问[网络安全概述](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>开始之前

本方案将已知合理的基准与针对虚拟机返回的安全组视图结果进行比较。

本方案假设已根据[创建网络观察程序](network-watcher-create.md)中的步骤创建了网络观察程序。 此外，本方案假设要使用一个包含有效虚拟机的资源组。

## <a name="scenario"></a>方案

本文所述的方案将获取虚拟机的安全组视图。

本方案中的操作：

- 检索已知合理的规则集
- 使用 REST API 检索虚拟机
- 获取虚拟机的安全组视图
- 评估响应

## <a name="retrieve-rule-set"></a>检索规则集

本示例的第一个步骤是处理现有的基准。 以下示例是使用 `Get-AzureRmNetworkSecurityGroup` cmdlet 从现有网络安全组中提取的、用作本示例基准的一些 JSON 代码。

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>将规则集转换为 PowerShell 对象

在此步骤中，我们将要读取一个 JSON 文件，该文件是前面使用应该对本示例中的网络安全组应用的规则创建的。

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>检索网络观察程序

下一步是检索网络观察程序实例。 将 `$networkWatcher` 变量传递给 `AzureRmNetworkWatcherSecurityGroupView` cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>获取 VM

需要针对虚拟机运行 `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet。 以下示例获取 VM 对象。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>检索安全组视图

下一步是检索安全组视图结果。 将此结果与前面所示的“基准”JSON 进行比较。

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>分析结果

响应已按网络接口分组。 返回的不同类型的规则是有效的默认安全规则。 结果已按其应用方式（针对子网或虚拟 NIC 应用）进一步细分。

以下 PowerShell 脚本将安全组视图的结果与 NSG 的现有输出进行比较。 以下简单示例演示如何使用 `Compare-Object` cmdlet 来比较结果。

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

以下示例为结果。 可以看到，第一个规则集中的两条规则未出现在比较结果中。

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>后续步骤

如果更改了设置，请参阅[管理网络安全组](../virtual-network/virtual-network-manage-nsg-arm-portal.md)来跟踪有问题的网络安全组和安全规则。














