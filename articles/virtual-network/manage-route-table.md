---
title: "创建、更改或删除 Azure 路由表 | Microsoft Docs"
description: "了解如何创建、更改或删除路由表。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 7edc73f337a72c24fd24d94468ee590b75dfa7df
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
---
# <a name="create-change-or-delete-a-route-table"></a>创建、更改或删除路由表

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。 如果你不熟悉 Azure 路由，我们建议在完成本文中所述的任务之前，先阅读[路由概述](virtual-networks-udr-overview.md)并完成[使用路由表路由网络流量](create-user-defined-route-portal.md)教程。

## <a name="before-you-begin"></a>开始之前

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 5.2.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.26 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

## <a name="create-a-route-table"></a>创建路由表

在每个 Azure 位置和订阅中可创建的路由表数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户左上角选择“+ 新建”。
2. 依次选择“网络”、“路由表”。
3. 输入路由表的**名称**，选择自己的**订阅**，创建新的**资源组**或选择现有的资源组，选择一个**位置**，然后选择“创建”。 “禁用 BGP 路由传播”选项会阻止通过 BGP 将本地路由传播到 Azure 虚拟网络。 如果虚拟网络未连接到 Azure 网络网关（VPN 或 ExpressRoute），请清除“已禁用”选项。 

**命令**

- Azure CLI：[az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell：[New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>查看路由表

在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。 随后将列出订阅中存在的路由表。

**命令**

- Azure CLI：[az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell：[Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>查看路由表详细信息

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 在列表中选择要查看其详细信息的路由表。 在“设置”下，可以查看路由表中的**路由**，以及与该路由表关联的**子网**。
3. 若要详细了解常见的 Azure 设置，请参阅以下信息：
    *   [活动日志](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [访问控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [标记](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [锁](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [自动化脚本](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**命令**

- Azure CLI：[az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell：[Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>更改路由表

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 选择要更改的路由表。 最常见的更改包括[添加](#create-a-route)或[删除](#delete-a-route)路由，以及在子网中[关联](#associate-a-route-table-to-a-subnet)路由表或[取消关联](#dissociate-a-route-table-from-a-subnet)路由表。

**命令**

- Azure CLI：[az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell：[Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

子网可以没有或有一个与其相关联的路由表。 一个路由表可与零个或多个子网相关联。 由于路由表不会关联到虚拟网络，因此，必须将路由表关联到每个相关子网。 如果虚拟网络已连接到 Azure 虚拟网络网关（ExpressRoute 或 VPN，如果结合 VPN 网关使用 BGP），则离开子网的所有流量将会根据路由表中创建的路由、[默认路由](virtual-networks-udr-overview.md#default)以及从本地网络传播的路由进行路由。 只能将路由表关联到该路由表所在的同一 Azure 位置和订阅中的虚拟网络内的子网。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请选择它。
2. 在列表中选择要将路由表关联到的子网所在的虚拟网络。
3. 在“设置”下选择“子网”。
4. 选择要将路由表关联到的子网。
5. 依次选择“路由表”、要关联到子网的路由表、“保存”。

**命令**

- Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell：[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>从子网取消关联路由表

从子网取消关联路由表后，Azure 会根据流量的[默认路由](virtual-networks-udr-overview.md#default)来路由流量。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请选择它。
2. 在列表中选择要从中取消关联路由表的子网所在的虚拟网络。
3. 在“设置”下选择“子网”。
4. 选择要从中取消关联路由表的子网。
5. 依次选择“路由表”、“无”、“保存”。

**命令**

- Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell：[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>删除路由表

如果路由表已关联到任何子网，则无法删除它。 在尝试删除路由表之前，请从所有子网[取消关联](#dissociate-a-route-table-from-a-subnet)该路由表。

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 选择要删除的路由表右侧的“...”。
3. 依次选择“删除”、“是”。

**命令**

- Azure CLI：[az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell：[Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>创建路由

在每个 Azure 位置和订阅中，可为每个路由表创建的路由数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 在列表中选择要将路由添加到的路由表。
3. 在“设置”下选择“路由”。
4. 选择“+ 添加”。
5. 输入该路由在路由表中的唯一**名称**。
6. 以 CIDR 表示法输入要将流量路由到的**地址前缀**。 该前缀不能在路由表的多个路由中重复，不过，可以包含在另一个前缀中。 例如，如果在一个路由中定义了 10.0.0.0/16 作为前缀，仍可使用 10.0.0.0/24 地址前缀定义另一个路由。 Azure 根据最长的前缀匹配项选择流量的路由。 若要详细了解 Azure 如何选择路由，请参阅[路由概述](virtual-networks-udr-overview.md#how-azure-selects-a-route)。
7. 选择“下一跃点类型”。 有关所有下一跃点类型的详细说明，请参阅[路由概述](virtual-networks-udr-overview.md)。
8. 输入“下一跃点地址”的 IP 地址。 如果为“下一跃点类型”选择了“虚拟设备”，则只能输入一个地址。
9. 选择“确定”。 

**命令**

- Azure CLI：[az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell：[New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>查看路由

一个路由表包含零个或多个路由。 若要详细了解查看路由时所列的信息，请参阅[路由概述](virtual-networks-udr-overview.md)。

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 在列表中选择要查看其路由的路由表。
3. 在“设置”下选择“路由”。

**命令**

- Azure CLI：[az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell：[Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>查看路由详细信息

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 在列表中选择要查看其路由详细信息的路由表。
3. 选择“路由”。
4. 选择要查看其详细信息的路由。

**命令**

- Azure CLI：[az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell：[Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>更改路由

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 选择要更改其路由的路由表。
3. 选择“路由”。
4. 选择要更改的路由。
5. 将现有设置更改为新设置，然后选择“保存”。

**命令**

- Azure CLI：[az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell：[Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>删除路由

1. 在门户顶部的搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。
2. 选择要删除其路由的路由表。
3. 选择“路由”。
4. 在路由列表中，选择要删除的路由右侧的“...”。
5. 依次选择“删除”、“是”。

**命令**

- Azure CLI：[az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell：[Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>查看有效路由

附加到虚拟机的每个网络接口的有效路由是创建的路由表、Azure 的默认路由，以及通过 BGP 和 Azure 虚拟网络网关从本地网络传播的任何路由的组合。 排查路由问题时，了解网络接口的有效路由非常有用。 可以查看已附加到运行中虚拟机的任何网络接口的有效路由。

1. 在门户顶部的搜索框中，输入要查看其有效路由的虚拟机的名称。 如果不知道虚拟机的名称，请在搜索框中输入“虚拟机”。 当“虚拟机”出现在搜索结果中时，请选择它，并从列表中选择虚拟机。
2. 在“设置”下选择“网络”。
3. 选择网络接口的名称。
4. 在“支持 + 故障排除”下，选择“有效路由”。
5. 查看有效路由的列表，以确定是否存在可用于路由流量的正确路由。 [路由概述](virtual-networks-udr-overview.md)中详细介绍了此列表中显示的下一跃点类型。

**命令**

- Azure CLI：[az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>验证两个终结点之间的路由

可以确定虚拟机与另一 Azure 资源的 IP 地址、本地资源或 Internet 上某个资源之间的下一跃点类型。 排查路由问题时，确定 Azure 的路由很有帮助。 若要完成此任务，必须使用现有的网络观察程序。 如果没有网络观察程序，可以完成[创建网络观察程序实例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤来创建一个。

1. 在门户顶部的搜索框中，输入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
2. 在“网络诊断工具”下选择“下一跃点”。
3. 选择要从其验证路由的源虚拟机的**订阅**和**资源组**。
4. 依次选择“虚拟机”、附加到虚拟机的**网络接口**，以及分配给要从其验证路由的网络接口的**源 IP 地址**。
5. 输入要验证的路由**目标 IP 地址**。
6. 选择“下一跃点”。
7. 片刻之后，系统会返回信息，告知路由了流量的路由的下一跃点类型和 ID。 [路由概述](virtual-networks-udr-overview.md)中详细介绍了返回的下一跃点类型。

**命令**

- Azure CLI：[az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell：[Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>权限

若要针对路由表和路由执行任务，必须向帐户分配到[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配到下表中列出适当的权限[自定义](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

|Operation                                                       |   操作名称                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   获取路由表                              |
|Microsoft.Network/routeTables/write                             |   创建或更新路由表                 |
|Microsoft.Network/routeTables/delete                            |   删除路由表                           |
|Microsoft.Network/routeTables/join/action                       |   联接路由表                             |
|Microsoft.Network/routeTables/routes/read                       |   获取路由                                    |
|Microsoft.Network/routeTables/routes/write                      |   创建或更新路由                       |
|Microsoft.Network/routeTables/routes/delete                     |   删除路由                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   获取网络接口的有效路由表  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   从 VM 获取下一跃点                  |

需要使用“联接路由表”操作将路由表关联到子网。