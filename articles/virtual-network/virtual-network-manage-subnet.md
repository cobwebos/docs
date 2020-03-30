---
title: 添加、更改或删除 Azure 虚拟网络子网
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 中添加、更改或删除虚拟网络子网。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246936"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>添加、更改或删除虚拟网络子网

了解如何添加、更改或删除虚拟网络子网。 部署到虚拟网络的所有 Azure 资源都将部署到虚拟网络内的子网中。 如果您是虚拟网络的新增功能，则可以在[虚拟网络概述](virtual-networks-overview.md)或完成[快速入门](quick-create-portal.md)中了解有关它们的详细信息。 要了解有关管理虚拟网络的更多信息，请参阅[创建、更改或删除虚拟网络](manage-virtual-network.md)。

## <a name="before-you-begin"></a>开始之前

如果没有，则使用活动订阅设置 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 然后，在本文任何部分中启动步骤之前完成这些任务之一： 

- **门户用户**：使用 Azure 帐户登录到[Azure 门户](https://portal.azure.com)。

- **PowerShell 用户**：在[Azure 云壳](https://shell.azure.com/powershell)中运行命令，或者从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 在 Azure 云壳浏览器选项卡中，查找 **"选择环境**"下拉列表，如果尚未选择 **"PowerShell"，请选择 PowerShell。**

    如果在本地运行 PowerShell，请使用 Azure PowerShell 模块版本 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az.Network` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 还要运行`Connect-AzAccount`以创建与 Azure 的连接。

- **Azure 命令行接口 （CLI） 用户**：在 Azure[云外壳](https://shell.azure.com/bash)中运行命令，或从计算机运行 CLI。 如果您在本地运行 Azure CLI，请使用 Azure CLI 版本 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 还要运行`az login`以创建与 Azure 的连接。

必须将登录到或连接到 Azure 的帐户分配给[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已分配[权限](#permissions)中列出的相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="add-a-subnet"></a>添加子网

1. 转到[Azure 门户](https://portal.azure.com)以查看虚拟网络。 搜索并选择**虚拟网络**。

2. 选择要向其添加子网的虚拟网络的名称。

3. 从 **"设置"** 中，选择**子** > **网子网**。

4. 在 **"添加子网"** 对话框中，输入以下设置的值：

    | 设置 | 描述 |
    | --- | --- |
    | **名称** | 该名称必须在虚拟网络中唯一。 为了最大程度地兼容其他 Azure 服务，我们建议使用字母作为名称的第一个字符。 例如，Azure 应用程序网关不会部署到名称以数字开头的子网中。 |
    | **地址范围** | <p>此范围在该虚拟网络的地址空间内必须唯一。 范围不能与虚拟网络中的其他子网地址范围重叠。 必须使用无类别域际路由选择 (CIDR) 表示法指定地址空间。</p><p>例如，在地址空间*为 10.0.0.0/16*的虚拟网络中，可以定义*10.0.0.0/22*的子网地址空间。 可以指定的最小范围是 */29*，它为子网提供八个 IP 地址。 Azure 保留每个子网中的第一个地址和最后一个地址，以确保协议一致性。 此外还会保留三个地址供 Azure 服务使用。 因此，使用 */29*地址范围定义子网会导致子网中出现三个可用的 IP 地址。</p><p>如果打算将虚拟网络连接到 VPN 网关，必须创建一个网关子网。 详细了解[网关子网地址范围具体考虑事项](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 在特定条件下，可在添加子网后更改地址范围。 若要了解有关如何更改子网地址范围的相关信息，请参阅[更改子网设置](#change-subnet-settings)。</p> |
    | **网络安全组** | 要筛选子网的入站和出站网络流量，可以将现有网络安全组关联到子网。 网络安全组必须存在于与虚拟网络相同的订阅和位置。 若要详细了解[网络安全组](security-overview.md)，请参阅[如何创建网络安全组](tutorial-filter-network-traffic.md)。 |
    | **路由表** | 要控制网络流量路由到其他网络，可以选择将现有路由表关联到子网。 路由表必须存在于与虚拟网络相同的订阅和位置。 详细了解[Azure 路由](virtual-networks-udr-overview.md)[以及如何创建路由表](tutorial-create-route-table-portal.md)。 |
    | **服务终结点** | <p>子网可以选择为其启用一个或多个服务终结点。 若要启用的服务的服务终结点，选择的服务或服务，想要启用服务终结点从**服务**列表。 Azure 自动配置终结点的位置。 默认情况下，Azure 配置虚拟网络区域的服务终结点。 为了支持区域故障转移方案，Azure 会自动将终结点配置为[Azure 配对区域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)，以便进行 Azure 存储。</p><p>若要删除的服务终结点，请取消选择你想要删除的服务终结点的服务。 要了解有关服务终结点及其可为其启用的服务的更多信息，请参阅[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)。 一旦启用服务的服务终结点，还必须启用与服务创建的资源的子网的网络访问权限。 例如，如果启用的服务终结点**Microsoft.Storage**，还必须启用到你想要授予对网络访问权限的所有 Azure 存储帐户的网络访问权限。 要启用对启用服务终结点的子网的网络访问，请参阅为服务终结点启用的各个服务的文档。</p><p>要验证是否为某个子网启用了服务终结点，请查看[有效路由](diagnose-network-routing-problem.md)，获取该子网中的任何网络接口。 配置终结点时，您将看到一个*默认*路由，其中带有服务的地址前缀，另一个跃点类型的**虚拟网络服务终结点**。 要了解有关路由的更多信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子网委派** | 子网可以选择启用一个或多个代表团。 子网委派向服务授予显式权限，用于在服务部署期间使用唯一标识符在子网中创建特定于服务的资源。 若要向服务委派，请从“服务”**** 列表中选择要委派给的服务。 |

5. 单击“确定”，将子网添加到所选的虚拟网络。****

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>更改子网设置

1. 转到[Azure 门户](https://portal.azure.com)以查看虚拟网络。 搜索并选择**虚拟网络**。

2. 选择包含要更改的子网的虚拟网络的名称。

3. 从 **"设置"** 中，选择**子网**。

4. 在子网的列表中，选择想要更改设置的子网。

5. 在子网页中，更改以下任一设置：

    | 设置 | 描述 |
    | --- | --- |
    | **地址范围** | 如果没有资源部署在子网内，可以更改的地址范围。 如果子网中存在的任何资源，必须将资源移到另一个子网，或从子网中先删除它们。 删除资源所采取的步骤因资源而异。 要了解如何移动或删除子网中的资源，请阅读每种资源类型的文档。 请参阅[添加子网](#add-a-subnet)的步骤 4 中**的地址范围**的约束。 |
    | **用户** | 可使用内置角色或自己的自定义角色控制对子网的访问。 要了解有关分配角色和用户以访问子网的更多内容，请参阅[添加角色分配](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)。 |
    | **网络安全组**和**路由表** | 请参阅[添加子网](#add-a-subnet)的步骤 4。 |
    | **服务终结点** | <p>请参阅[添加子网](#add-a-subnet)的步骤 4 中的服务终结点。 如果为某个现有子网启用服务终结点，请确保该子网的任何资源上未运行任何关键任务。 服务终结点在子网中的每个网络接口上切换路由。 服务终结点从使用具有*0.0.0/0*地址前缀和下一个跃点类型的*Internet*的默认路由，到使用具有服务地址前缀和下一个跃点类型的*虚拟网络服务终结点*。</p><p>切换过程中，可能会终止任何打开的 TCP 连接。 在使用新路由更新所有网络接口的服务流量之前，不会启用服务终结点。 要了解有关路由的更多信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。</p> |
    | **子网委派** | 请参阅[添加子网](#add-a-subnet)的步骤 4 中的服务终结点。 可以将子网委派修改为，为子网启用零到多个委派。 如果服务的资源已部署在子网中，则在删除服务的所有资源之前，无法添加或删除子网委派。 若要向其他服务委派，请从“服务”**** 列表中选择要委派给的服务。 |

6. 选择“保存”。****

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>删除子网

仅当子网中无任何资源时，才可删除该子网。 如果资源位于子网中，则必须删除这些资源，然后才能删除子网。 删除资源所采取的步骤因资源而异。 要了解如何删除子网中的资源，请阅读每种资源类型的文档。

1. 转到[Azure 门户](https://portal.azure.com)以查看虚拟网络。 搜索并选择**虚拟网络**。

2. 选择包含要删除的子网的虚拟网络的名称。

3. 从 **"设置"** 中，选择**子网**。

4. 在子网列表中，选择要删除的子网。

5. 选择 **"删除**"，然后在确认对话框中选择 **"是**"。

### <a name="commands"></a>命令

| 工具 | 命令 |
| ---- | ------- |
| Azure CLI | [az 网络 vnet 子网删除](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>权限

要在子网上执行任务，您的帐户必须分配给[网络参与者角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)或已在下表中分配相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

|操作                                                                   |   “属性”                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   读取虚拟网络子网              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   创建或更新虚拟网络子网  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   删除虚拟网络子网            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   加入虚拟网络                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   为子网启用服务终结点     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   获取子网中的虚拟机       |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建虚拟网络和子网
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
