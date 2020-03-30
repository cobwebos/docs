---
title: 创建、更改或删除 Azure 路由表
titlesuffix: Azure Virtual Network
description: 了解如何创建、更改或删除路由表。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247055"
---
# <a name="create-change-or-delete-a-route-table"></a>创建、更改或删除路由表

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。 如果您是虚拟网络中路由的新功能，则可以在[虚拟网络流量路由](virtual-networks-udr-overview.md)或完成[教程](tutorial-create-route-table-portal.md)中了解有关它的更多信息。

## <a name="before-you-begin"></a>开始之前

如果没有，则使用活动订阅设置 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然后，在本文任何部分中启动步骤之前完成这些任务之一：

- **门户用户**：使用 Azure 帐户登录到[Azure 门户](https://portal.azure.com)。

- **PowerShell 用户**：在[Azure 云壳](https://shell.azure.com/powershell)中运行命令，或者从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 在 Azure 云壳浏览器选项卡中，查找 **"选择环境**"下拉列表，如果尚未选择 **"PowerShell"，请选择 PowerShell。**

    如果在本地运行 PowerShell，请使用 Azure PowerShell 模块版本 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az.Network` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 还要运行`Connect-AzAccount`以创建与 Azure 的连接。

- **Azure 命令行接口 （CLI） 用户**：在 Azure[云外壳](https://shell.azure.com/bash)中运行命令，或从计算机运行 CLI。 如果您在本地运行 Azure CLI，请使用 Azure CLI 版本 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 还要运行`az login`以创建与 Azure 的连接。

必须将您登录或连接到 Azure 的帐户分配给[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[权限](#permissions)中列出的相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-route-table"></a>创建路由表

每个 Azure 位置和订阅可以创建多少个路由表是有限制的。 有关详细信息，请参阅[网络限制 - Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在[Azure 门户](https://portal.azure.com)菜单或**主页**上，选择 **"创建资源**"。

1. 在搜索框中，输入“路由表”**。 当“路由表”出现在搜索结果中时，请选择它。****

1. 在“路由表”**** 页中，选择“创建”****。

1. 在 **"创建路由表"** 对话框中：

    1. 输入路由表**的名称**。
    1. 选择您的**订阅**。
    1. 选择现有**资源组**或选择 **"新建**"以创建新资源组。
    1. 选择“位置”****。
    1. 如果计划将路由表关联到通过 VPN 网关连接到本地网络的虚拟网络中的子网，并且不希望将本地路由传播到子网中的网络接口，请将**虚拟网络网关路由传播**设置为 **"已禁用"。**

1. 选择 **"创建**"以创建新的路由表。

### <a name="create-route-table---commands"></a>创建路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>查看路由表

转到[Azure 门户](https://portal.azure.com)以管理虚拟网络。 搜索并选择**路由表**。 随后将列出订阅中存在的路由表。

### <a name="view-route-table---commands"></a>查看路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表列表](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>查看路由表详细信息

1. 转到[Azure 门户](https://portal.azure.com)以管理虚拟网络。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择要查看其详细信息的路由表。

1. 在工艺路线表页中，**在"设置"** 下，查看路由表中的**路由**或路由表关联的**子网**。

若要详细了解常见的 Azure 设置，请参阅以下信息：

- [活动日志](../azure-monitor/platform/platform-logs-overview.md)
- [访问控制 （IAM）](../role-based-access-control/overview.md)
- [标记](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [锁](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [自动化脚本](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>查看路由表详细信息 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表显示](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>更改路由表

1. 转到[Azure 门户](https://portal.azure.com)以管理虚拟网络。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择要更改的路由表。

最常见的更改是[添加](#create-a-route)路由、[删除](#delete-a-route)路由、将路由表[关联](#associate-a-route-table-to-a-subnet)到子网或从子网[分离](#dissociate-a-route-table-from-a-subnet)路由表。

### <a name="change-a-route-table---commands"></a>更改路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表更新](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

您可以选择将路由表关联到子网。 路由表可以关联到零个或多个子网。 由于路由表未与虚拟网络关联，因此必须将路由表关联到要关联的路由表的每个子网。 如果虚拟网络连接到 Azure 虚拟网络网关（ExpressRoute 或 VPN），则 Azure 会路由基于您在路由表、[默认路由](virtual-networks-udr-overview.md#default)和从本地网络传播的路由离开子网的所有流量。 只能将路由表关联到该路由表所在的同一 Azure 位置和订阅中的虚拟网络内的子网。

1. 转到[Azure 门户](https://portal.azure.com)以管理虚拟网络。 搜索并选择**虚拟网络**。

1. 在虚拟网络列表中，选择包含要将路由表关联的子网的虚拟网络。

1. 在虚拟网络菜单栏中，选择**子网**。

1. 选择要将路由表关联到的子网。

1. 在**路由表中**，选择要关联到子网的路由表。

1. 选择“保存”。****

如果您的虚拟网络已连接到 Azure VPN 网关，请不要将路由表关联到网关[子网，该子网](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)包含目标*为 0.0.0.0/0*的路由。 这样做可能会阻止网关正常工作。 有关在路由中使用*0.0.0.0/0*的详细信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md#default-route)。

### <a name="associate-a-route-table---commands"></a>关联路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>从子网取消关联路由表

从子网取消关联路由表后，Azure 会根据流量的[默认路由](virtual-networks-udr-overview.md#default)来路由流量。

1. 转到[Azure 门户](https://portal.azure.com)以管理虚拟网络。 搜索并选择**虚拟网络**。

1. 在虚拟网络列表中，选择包含要从路由表分离的子网的虚拟网络。

1. 在虚拟网络菜单栏中，选择**子网**。

1. 选择要从中取消关联路由表的子网。

1. 在 **"路由"表中**，选择 **"无**"。

1. 选择“保存”。****

### <a name="dissociate-a-route-table---commands"></a>取消关联路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>删除路由表

不能删除与任何子网关联的路由表。 在尝试删除路由表之前，请从所有子网[取消关联](#dissociate-a-route-table-from-a-subnet)该路由表。

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择要删除的路由表。

1. 选择 **"删除**"，然后在确认对话框中选择 **"是**"。

### <a name="delete-a-route-table---commands"></a>删除路由表 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表删除](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>创建路由

每个路由表每个 Azure 位置和订阅可以创建多少路由是有限制的。 有关详细信息，请参阅[网络限制 - Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择要向其添加路由的路由表。

1. 从路由表菜单栏中，选择 **"路径** > **添加**"。

1. 在工艺路线表中输入路由的唯一**路由名称**。

1. 输入**地址前缀**，在要将流量路由到的无类域间路由 （CIDR） 表示法中。 前缀不能在工艺路线表中的多个路由中复制，尽管前缀可以位于另一个前缀中。 例如，如果将*10.0.0.0/16*定义为一个路由中的前缀，则仍可以使用*10.0.0.0/22*地址前缀定义另一个路由。 Azure 根据最长的前缀匹配项选择流量的路由。 要了解详细信息，请参阅[Azure 如何选择路由](virtual-networks-udr-overview.md#how-azure-selects-a-route)。

1. 选择下一个**跃点类型**。 要了解有关下一个跃点类型的更多信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。

1. 如果选择了 **"下一个跃点"类型的****虚拟设备**，请输入**下一个跃点地址**的 IP 地址。

1. 选择“确定”。

### <a name="create-a-route---commands"></a>创建路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>查看路由

一个路由表包含零个或多个路由。 要了解有关查看路由时列出的信息的详细信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择要为其查看路由的路由表。

1. 在工艺路线表菜单栏中，选择 **"路由"** 以查看路由列表。

### <a name="view-routes---commands"></a>查看路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表路由列表](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>查看路由详细信息

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择包含要查看其详细信息的路由表。

1. 在工艺路线表菜单栏中，选择 **"路由"** 以查看路由列表。

1. 选择要查看其详细信息的路由。

### <a name="view-details-of-a-route---commands"></a>查看路由详细信息 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表路由显示](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>更改路由

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择包含要更改的路由的路由表。

1. 在工艺路线表菜单栏中，选择 **"路由"** 以查看路由列表。

1. 选择要更改的路线。

1. 将现有设置更改为新设置，然后选择“保存”。****

### <a name="change-a-route---commands"></a>更改路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表路由更新](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>删除路由

1. 转到[Azure 门户](https://portal.azure.com)以管理路由表。 搜索并选择**路由表**。

1. 在工艺路线表列表中，选择包含要删除的路由的路由表。

1. 在工艺路线表菜单栏中，选择 **"路由"** 以查看路由列表。

1. 选择要删除的路线。

1. 选择 **"删除**"，然后在确认对话框中选择 **"是**"。

### <a name="delete-a-route---commands"></a>删除路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络路由表路由删除](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>查看有效路由

每个 VM 连接的网络接口的有效路由是已创建的路由表、Azure 的默认路由以及通过边界网关协议 （BGP） 通过 Azure 虚拟网络从本地网络传播的任何路由的组合网关。 排查路由问题时，了解网络接口的有效路由非常有用。 您可以查看连接到正在运行的 VM 的任何网络接口的有效路由。

1. 转到[Azure 门户](https://portal.azure.com)以管理 VM。 搜索并选择**虚拟机**。

1. 在虚拟机列表中，选择要查看有效路由的 VM。

1. 在 VM 菜单栏中，选择 **"网络**"。

1. 选择网络接口的名称。

1. 在网络界面菜单栏中，选择**有效路由**。

1. 查看有效路由的列表，查看是否存在要将流量路由到的位置的正确路由。 详细了解您在[虚拟网络流量路由](virtual-networks-udr-overview.md)中在此列表中看到的下一个跃点类型。

### <a name="view-effective-routes---commands"></a>查看有效路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络 nic 显示有效路由表](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>验证两个终结点之间的路由

可以确定虚拟机与另一 Azure 资源的 IP 地址、本地资源或 Internet 上某个资源之间的下一跃点类型。 排查路由问题时，确定 Azure 的路由很有帮助。 要完成此任务，您必须具有现有的网络观察程序。 如果没有现有的网络观察程序，请通过完成["创建网络观察程序"实例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤来创建一个。

1. 转到[Azure 门户](https://portal.azure.com)以管理网络观察程序。 搜索并选择**网络观察程序**。

1. 在网络观察程序菜单栏中，选择 **"下一个跃点**"。

1. 在**网络观察程序中 |下一个跃点**页面：

    1. 选择要从中验证路由的**订阅**和资源**组**。

    1. 选择连接到 VM 的**虚拟机****和网络接口**。
    
    1. 输入分配给要验证路由的网络接口的**源 IP 地址**。

    1. 输入要验证路由**到的目标 IP 地址**。

1. 选择“下一跃点”。****

稍等片刻后，Azure 会告诉您下一个跃点类型以及路由流量的路由的 ID。 详细了解您在[虚拟网络流量路由](virtual-networks-udr-overview.md)中返回的下一个跃点类型。

### <a name="validate-routing-between-two-endpoints---commands"></a>验证两个终结点之间的路由 - 命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络观察程序显示下一跳](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>权限

要执行路由表和路由上的任务，您的帐户必须分配给[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配下表中列出的相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 操作                                                          |   “属性”                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   读取路由表                                    |
| Microsoft.Network/routeTables/write                             |   创建或更新路由表                        |
| Microsoft.Network/routeTables/delete                            |   删除路由表                                  |
| Microsoft.Network/routeTables/join/action                       |   将路由表关联到子网                   |
| Microsoft.Network/routeTables/routes/read                       |   读取路由                                          |
| Microsoft.Network/routeTables/routes/write                      |   创建或更新路由                              |
| Microsoft.Network/routeTables/routes/delete                     |   删除路由                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   为网络接口获取有效路由表 |
| Microsoft.Network/networkWatchers/nextHop/action                |   从 VM 获取下一跃点                           |

## <a name="next-steps"></a>后续步骤

- 使用[PowerShell](powershell-samples.md)或[Azure CLI](cli-samples.md)示例脚本或 Azure[资源管理器模板](template-samples.md)创建路由表
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
