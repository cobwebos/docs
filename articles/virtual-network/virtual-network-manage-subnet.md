---
title: 添加、更改或删除 Azure 虚拟网络子网
titlesuffix: Azure Virtual Network
description: 了解在何处可以找到有关虚拟网络的信息，以及如何在 Azure 中添加、更改或删除虚拟网络子网。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87286098"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>添加、更改或删除虚拟网络子网

了解如何添加、更改或删除虚拟网络子网。 部署到虚拟网络的所有 Azure 资源都将部署到虚拟网络内的子网中。 如果不熟悉虚拟网络，可在[虚拟网络概述](virtual-networks-overview.md)中或通过完成[入门](quick-create-portal.md)了解相关详细信息。 若要详细了解管理虚拟网络，请参阅[创建、更改或删除虚拟网络](manage-virtual-network.md)。

## <a name="before-you-begin"></a>准备阶段

如果你没有 Azure 帐户，请使用有效的订阅设置一个帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然后在开始执行本文任一部分中的步骤之前完成以下任务之一： 

- **门户用户**：使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

- **PowerShell 用户**：运行 [Azure Cloud Shell](https://shell.azure.com/powershell)中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 在 "Azure Cloud Shell 浏览器" 选项卡中，找到 " **选择环境** " 下拉列表，然后选择 " **PowerShell** " （如果尚未选择）。

    如果在本地运行 PowerShell，请使用 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az.Network` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 另请运行 `Connect-AzAccount` 以创建与 Azure 的连接。

- **Azure 命令行接口 (CLI) 用户**：在 [Azure Cloud Shell](https://shell.azure.com/bash)中运行命令，或从计算机运行 cli。 如果在本地运行 Azure CLI，请使用 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 另请运行 `az login` 以创建与 Azure 的连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="add-a-subnet"></a>添加子网

1. 转到 [Azure 门户](https://portal.azure.com)来查看虚拟网络。 搜索并选择“虚拟网络”。

2. 选择要将子网添加到的虚拟网络的名称。

3. 在“设置”中，选择“子网” > “子网”**** **** ****。

4. 在“添加子网”对话框中，输入以下设置的值****：

    | 设置 | 说明 |
    | --- | --- |
    | **名称** | 名称在虚拟网络中必须唯一。 为了最大程度地兼容其他 Azure 服务，我们建议使用字母作为名称的第一个字符。 例如，Azure 应用程序网关不会部署到名称以数字开头的子网中。 |
    | **地址范围** | <p>此范围在虚拟网络的地址空间中必须唯一。 此范围不能与虚拟网络中的其他子网地址范围重叠。 必须使用无类域间路由 (CIDR) 表示法指定地址空间。</p><p>例如，在地址空间为 10.0.0.0/16 的虚拟网络中，可将子网地址空间定义为 10.0.0.0/22** **。 可以指定的最小范围为 /29，为子网提供八个 IP 地址**。 Azure 保留每个子网中的第一个地址和最后一个地址，以确保协议一致性。 此外还会保留三个地址供 Azure 服务使用。 因此，使用 /29 地址范围定义子网时，子网中会有三个可用 IP 地址**。</p><p>如果打算将虚拟网络连接到 VPN 网关，必须创建一个网关子网。 详细了解[网关子网地址范围具体考虑事项](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 在特定条件下，可在添加子网后更改地址范围。 若要了解有关如何更改子网地址范围的相关信息，请参阅[更改子网设置](#change-subnet-settings)。</p> |
    | **网络安全组** | 要筛选子网的入站和出站网络流量，可将现有网络安全组关联到子网。 网络安全组必须与虚拟网络位于同一订阅和位置中。 若要详细了解[网络安全组](security-overview.md)，请参阅[如何创建网络安全组](tutorial-filter-network-traffic.md)。 |
    | **路由表** | 要控制通往其他网络的网络流量路由，可以选择将现有路由表关联到子网。 路由表必须与虚拟网络位于同一订阅和位置中。 详细了解 [Azure 路由](virtual-networks-udr-overview.md)和[如何创建路由表](tutorial-create-route-table-portal.md)。 |
    | **服务终结点** | <p>子网可以选择为其启用一个或多个服务终结点。 若要启用的服务的服务终结点，选择的服务或服务，想要启用服务终结点从**服务**列表。 Azure 会自动配置终结点的位置。 默认情况下，Azure 会为虚拟网络所在的区域配置服务终结点。 为了支持区域性故障转移方案，Azure 自动将终结点配置为 azure 存储的 [azure 配对区域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) 。</p><p>若要删除的服务终结点，请取消选择你想要删除的服务终结点的服务。 如需详细了解服务终结点以及可为其启用的服务，请参阅[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)。 一旦启用服务的服务终结点，还必须启用与服务创建的资源的子网的网络访问权限。 例如，如果启用的服务终结点**Microsoft.Storage**，还必须启用到你想要授予对网络访问权限的所有 Azure 存储帐户的网络访问权限。 要启用到为启用服务终结点的子网的网络访问，请参阅各个启用的服务终结点的服务的文档。</p><p>要验证是否为某个子网启用了服务终结点，请查看[有效路由](diagnose-network-routing-problem.md)，获取该子网中的任何网络接口。 配置终结点时，你将会看到服务地址前缀和下一跃点类型为“VirtualNetworkServiceEndpoint”的“默认”路由******。 若要详细了解路由，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子网委派** | 子网可以选择为其启用一个或多个委派。 子网委派为服务提供了显式权限，以便能够在服务部署期间使用唯一标识符在子网中创建服务专属资源。 若要向服务委派，请从“服务”**** 列表中选择要委派给的服务。 |

5. 单击“确定”，将子网添加到所选的虚拟网络。****

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>更改子网设置

1. 转到 [Azure 门户](https://portal.azure.com)来查看虚拟网络。 搜索并选择“虚拟网络”。

2. 选择包含要更改的子网的虚拟网络的名称。

3. 从“设置”中选择“子网”**** ****。

4. 在子网的列表中，选择想要更改设置的子网。

5. 在“子网”页中，更改以下任意设置：

    | 设置 | 说明 |
    | --- | --- |
    | **地址范围** | 如果没有资源部署在子网内，可以更改地址范围。 如果子网中存在的任何资源，必须将资源移到另一个子网，或从子网中先删除它们。 删除资源所采取的步骤因资源而异。 若要了解如何删除子网中的资源，请阅读其中每个资源类型的文档。 请参阅[添加子网](#add-a-subnet)步骤 4 中的**地址范围**约束。 |
    | **用户** | 可以使用内置角色或自己的自定义角色控制对子网的访问。 若要详细了解如何分配访问子网的角色和用户，请参阅[添加角色分配](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)。 |
    | **网络安全组**和**路由表** | 请参阅[添加子网](#add-a-subnet)的步骤 4。 |
    | **服务终结点** | <p>请参阅[添加子网](#add-a-subnet)的步骤 4 中的服务终结点。 如果为某个现有子网启用服务终结点，请确保该子网的任何资源上未运行任何关键任务。 服务终结点在子网中的每个网络接口上切换路由。 服务终结点从使用地址前缀为 0.0.0.0/0 且下一跃点类型为 Internet 的默认路由，转到使用服务地址前缀且下一跃点类型为 VirtualNetworkServiceEndpoint 的新路由** ** **。</p><p>切换过程中，可能会终止任何打开的 TCP 连接。 对于使用新路由更新的所有网络接口，除非流量流向服务，否则不会启用服务终结点。 若要详细了解路由，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子网委派** | 请参阅[添加子网](#add-a-subnet)的步骤 4 中的服务终结点。 可以将子网委派修改为，为子网启用零到多个委派。 如果已在子网中部署服务的资源，只有在删除服务的所有资源后，才能添加或删除子网委派。 若要向其他服务委派，请从“服务”**** 列表中选择要委派给的服务。 |

6. 选择“保存” 。

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>删除子网

仅当子网中无任何资源时，才可删除该子网。 如果子网中存在资源，则必须先删除这些资源，才能删除该子网。 删除资源所采取的步骤因资源而异。 若要了解如何删除子网中的资源，请阅读其中每个资源类型的文档。

1. 转到 [Azure 门户](https://portal.azure.com)来查看虚拟网络。 搜索并选择“虚拟网络”。

2. 选择包含要删除的子网的虚拟网络的名称。

3. 从“设置”中选择“子网”**** ****。

4. 在子网的列表中，选择想要删除的子网。

5. 选择“删除”，然后在确认对话框中选择“是”。**** ****

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>权限

若要在子网中执行任务，必须将帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配到下表中的适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

|操作                                                                   |   名称                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   读取虚拟网络子网              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   创建或更新虚拟网络子网  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   删除虚拟网络子网            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   加入虚拟网络                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   为子网启用服务终结点     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   获取子网中的虚拟机       |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建虚拟网络和子网
- 为虚拟网络创建和分配 [Azure Policy 定义](policy-samples.md)
