---
title: 创建、更改或删除 Azure 网络安全组 | Microsoft Docs
description: 了解如何创建、更改或删除网络安全组。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 7a244a5dbb86b076f99975ad477d4062699270b5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894219"
---
# <a name="create-change-or-delete-a-network-security-group"></a>创建、更改或删除网络安全组

通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 如果不熟悉网络安全组，请参阅[网络安全组概述](security-overview.md)了解有关详细信息，并完成[筛选流量](tutorial-filter-network-traffic.md)教程，获得有关网络安全组的一些经验。

## <a name="before-you-begin"></a>开始之前

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.28 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

必须将登录或连接到 Azure 所用的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有“[权限](#permissions)”中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用网络安全组

对于网络安全组可执行创建、[查看所有](#view-all-network-security-groups)、[查看详细信息](#view-details-of-a-network-security-group)[更改](#change-a-network-security-group)以及[删除](#delete-a-network-security-group)操作。 也可从网络接口或子网[关联或取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource)网络安全组。

### <a name="create-a-network-security-group"></a>创建网络安全组

在每个 Azure 位置和订阅中可创建的网络安全组数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户左上角选择“+ 创建资源”。
2. 依次选择“网络”、“网络安全组”。
3. 输入网络资源组的“名称”，选择自己的“订阅”，创建新的“资源组”或选择现有的资源组，选择一个“位置”，然后选择“创建”。

**命令**

- Azure CLI: [az network vnet create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>查看所有网络安全组

在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。 随后将列出订阅中存在的网络安全组。

**命令**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>查看网络安全组的详细信息

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 在列表中选择要查看其详细信息的网络安全组。 在“设置”下，可查看“入站安全规则”和“出站安全规则”以及与网络安全组相关联的“网络接口”和“子网”。 也可启用或禁用“诊断日志”和查看“有效的安全规则”。 要了解详细信息，请参阅[诊断日志](virtual-network-nsg-manage-log.md)和[查看有效的安全规则](virtual-network-nsg-troubleshoot-portal.md)。
3. 要了解有关列出的常见 Azure 设置的详细信息，请参阅以下文章：
    *   [活动日志](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [访问控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [标记](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [锁](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [自动化脚本](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**命令**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>更改网络安全组

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 选择要更改的网络安全组。 最常见的更改是[添加](#create-a-security-rule)或[删除](#delete-a-security-rule)安全规则以及[将网络安全组关联到子网或网络接口或从其中取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

**命令**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>将网络安全组与子网或网络接口关联或取消关联

要将网络安全组关联到网络接口，或从网络接口取消关联网络安全组，请参阅[将网络安全组与网络接口关联或取消关联](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 要将网络安全组关联到子网，或从子网取消关联网络安全组，请参阅[更改子网设置](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>删除网络安全组

如果网络安全组与任何子网或网络接口相关联，则无法删除。 从所有子网和网络接口[取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource)网络安全组，然后再尝试将其删除。

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 从列表中选择要删除的网络安全组。
3. 依次选择“删除”、“是”。

**命令**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>使用安全规则

网络安全组包含零个或多个安全规则。 对于安全规则，可执行创建、[查看所有](#view-all-security-rules)、[查看详细信息](#view-details-of-a-security-rule)[更改](#change-a-security-rule)以及[删除](#delete-a-security-rule)操作。

### <a name="create-a-security-rule"></a>创建安全规则

在每个 Azure 位置和订阅的每个网络安全组可创建的规则数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 从列表中选择要添加安全规则的网络安全组。
3. 在“设置”下选择“入站安全规则”。 列出了几个现有规则。 某些规则可能尚未添加。 创建网络安全组时，会在其中创建几个默认安全规则。 要了解详细信息，请参阅[默认安全规则](security-overview.md#default-security-rules)。  无法删除默认安全规则，但可以使用更高优先级的规则将其覆盖。
4. <a name = "security-rule-settings"></a>选择“+ 添加”。  为以下设置选择或添加值，然后选择“确定”：
    
    |设置  |值  |详细信息  |
    |---------|---------|---------|
    |Source     | 选择“任何”、“IP 地址”或“服务标记”。        | 如果选择“IP 地址”，那么必须指定“源 IP 地址/CIDR 范围”。 可指定单个值或以逗号分隔的多个值的列表。 多个值的示例为 10.0.0.0/16, 192.188.1.1。 可指定的值的数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 如果选择“服务标记”，那么必须选择一个服务标记。 服务标记是 IP 地址类别的预定义标识符。 要了解有关可用服务标记以及每个标记表示的含义的详细信息，请参阅[服务标记](security-overview.md#service-tags)        |
    |源端口范围     | 指定单个端口（如 80）、端口范围（如 1024-65535）或单个端口和/或端口范围的以逗号分隔的列表（如 80, 1024-65535）。 输入星号可允许任何端口上的流量。 | 端口和范围指定规则允许或拒绝哪个端口流量。 可指定的端口数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。  |
    |目标     | 选择“任何”、“IP 地址”或“虚拟网络”。        | 如果选择“IP 地址”，那么必须指定“目标 IP 地址/CIDR 范围”。 类似于“源”和“源 IP 地址/CIDR 范围”，你可指定单个或多个地址或范围，并且可指定的数目有限制。 选择“虚拟网络”，它是一个服务标记，意味着流量可到虚拟网络地址空间内的所有 IP 地址。        |
    |目标端口范围     | 指定单个值或以逗号分隔的多个值的列表。 | 类似于“源端口范围”，可指定单个或多个端口和范围，并且可指定的数目有限制。 |
    |协议     | 选择“任何”、“TCP”或“UDP”。        |         |
    |操作     | 选择“允许”或“拒绝”。        |         |
    |Priority     | 输入一个介于 100-4096 之间的值，该值对于网络安全组内的所有安全规则都是唯一的。 |规则按优先顺序处理。 编号越低，优先级越高。 建议创建规则时在优先级数字之间留出空隙，例如 100, 200, 300。 留出空隙后，未来在需要使规则高于或低于现有规则时，可更轻松添加规则。         |
    |名称     | 网络安全组内规则的唯一名称。        |  名称最多可包含 80 个字符。 它必须以字母或数字开头，以字母、数字或下划线结尾，且仅可包含字母、数字、下划线、句点或连字符。       |
    |说明     | 可选说明。        |         |

    无法使用门户为“源”或“目标”设置指定[应用程序安全组](#work-with-application-security-groups)。 但是，可使用 Azure CLI 或 PowerShell 指定。 “出站安全规则”的设置与此类似，因此不单独进行说明。

**命令**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>查看所有安全规则

网络安全组包含零个或多个规则。 要详细了解有关查看规则时所列的信息，请参阅[网络安全组概述](security-overview.md)。

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 从列表中选择要查看其规则的网络安全组。
3. 在“设置”下选择“入站安全规则”或“出站安全规则”。

列表包含已创建的任何规则以及网络安全组[默认安全规则](security-overview.md#default-security-rules)。

**命令**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>查看安全规则的详细信息

1. 在门户顶部的搜索框中，输入“网络安全组”。 “网络安全组”出现在搜索结果中时，将其选中。
2. 选择要查看其安全规则详细信息的网络安全组。
3. 在“设置”下选择“入站安全规则”或“出站安全规则”。
4. 选择要查看其详细信息的规则。 有关所有设置的详细说明，请参阅[安全规则设置](#security-rule-settings)。

**命令**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>更改安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。
2. 根据需要更改设置，然后选择“保存”。 有关所有设置的详细说明，请参阅[安全规则设置](#security-rule-settings)。

**命令**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>删除安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。
2. 依次选择“删除”、“是”。

**命令**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>使用应用程序安全组

应用程序安全组包含零个或多个网络接口。 要了解详细信息，请参阅[应用程序安全组](security-overview.md#application-security-groups)。 无法使用门户中的应用程序安全组，但可以使用 PowerShell 或 Azure CLI。 应用程序安全组中的所有网络接口必须存在于同一虚拟网络中。 添加到应用程序安全组的第一个网络接口确定后续所有网络接口必须位于哪个虚拟网络中。 要了解如何将网络接口添加到应用程序安全组，请参阅[将网络接口添加到应用程序安全组](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>创建应用程序安全组

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>查看所有应用程序安全组

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>查看特定应用程序安全组的详细信息

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>更改应用程序安全组

虽然可以更改现有应用程序安全组的某些设置（如标记和权限），但无法更改其名称或位置。

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: No PowerShell cmdlet.

### <a name="delete-an-application-security-group"></a>删除应用程序安全组

如果应用程序安全组中有任何网络接口，则不能将其删除。 通过更改网络接口设置或删除网络接口，从应用程序安全组中移除所有网络接口。 有关详细信息，请参阅[在应用程序安全组中添加或删除网络接口](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)或[删除网络接口](virtual-network-network-interface.md#delete-a-network-interface)。

**命令**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>权限

若要在网络安全组、安全规则和应用程序安全组上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列相应权限的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

### <a name="network-security-groups"></a>网络安全组

| 操作                                                        |   名称                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   获取网络安全组                                          |
| Microsoft.Network/ruleTables/write                            |   创建或更新网络安全组                             |
| Microsoft.Network/ruleTables/delete                           |   删除网络安全组                                       |
| Microsoft.Network/ruleTables/join/action                      |   将网络安全组与子网或网络接口关联 |
| Microsoft.Network/ruleTables/rules/read                       |   获取规则                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   创建或更新规则                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   删除规则                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   获取网络接口有效的网络安全组              |
| Microsoft.Network/networkWatchers/nextHop/action              |   从 VM 获取下一跃点                                         |

### <a name="application-security-groups"></a>应用程序安全组

| 操作                                                                     | 名称                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 将 IP 配置加入到应用程序安全组中|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 将安全规则加入到应用程序安全组中    |
| Microsoft.Network/applicationSecurityGroups/read                           | 获取应用程序安全组                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 创建或更新应用程序安全组           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 删除应用程序安全组                     |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建网络或应用程序安全组
- 为虚拟网络创建并应用 [Azure 策略](policy-samples.md)