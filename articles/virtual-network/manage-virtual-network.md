---
title: 创建、更改或删除 Azure 虚拟网络
titlesuffix: Azure Virtual Network
description: 创建和删除虚拟网络并更改现有虚拟网络的设置，如 DNS 服务器和 IP 地址空间。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 5581a4c43f0b78dc8c14c44bfb1ded371a925fd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88706024"
---
# <a name="create-change-or-delete-a-virtual-network"></a>创建、更改或删除虚拟网络

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

了解如何创建和删除虚拟网络以及更改现有虚拟网络的设置，如 DNS 服务器和 IP 地址空间。 如果不熟悉虚拟网络，可在[虚拟网络概述](virtual-networks-overview.md)中或通过完成[教程](quick-create-portal.md)了解相关详细信息。 虚拟网络包含子网。 若要了解如何创建、更改和删除子网，请阅读[管理子网](virtual-network-manage-subnet.md)。

## <a name="before-you-begin"></a>准备阶段

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。
- 登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 选择“+ 创建资源” > “网络” > “虚拟网络”。**** **** ****
2. 为以下设置输入或选择值，然后选择“创建”****：
   - **名称**：该名称在选择创建虚拟网络的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中必须是唯一的。 虚拟网络创建后，无法更改名称。 可随着时间推移创建多个虚拟网络。 有关命名建议，请参阅[命名约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)。 遵循命名约定可以更轻松地管理多个虚拟网络。
   - **地址空间**：虚拟网络的地址空间由以 CIDR 表示法指定的一个或多个非重叠地址范围组成。 定义的地址范围可以是公共或专用 (RFC 1918) 地址。 无论是将地址范围定义为公用还是专用，地址范围都只能从虚拟网络内、从互联的虚拟网络以及从任何已连接到虚拟网络的本地网络进行访问。 无法添加以下的地址范围：
     - 224.0.0.0/4（多播）
     - 255.255.255.255/32（广播）
     - 127.0.0.0/8（环回）
     - 169.254.0.0/16（本地链路）
     - 168.63.129.16/32（内部 DNS、DHCP 和 Azure 负载均衡器[运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md#probesource)）

     尽管在门户中创建虚拟网络时只能定义一个地址范围，但可以在虚拟网络创建之后向地址空间添加更多地址范围。 若要了解如何将地址范围添加到现有的虚拟网络，请参阅[添加或删除地址范围](#add-or-remove-an-address-range)。

     > [!WARNING]
     > 如果虚拟网络的地址范围与其他虚拟网络或本地网络重叠，则这两个网络无法连接。 定义地址范围之前，请考虑将来是否会想要将此虚拟网络连接到其他虚拟网络或本地网络。 Microsoft 建议使用组织拥有的专用地址空间或公用地址空间配置虚拟网络地址范围。
     >

     - **子网名称**：子网名称在虚拟网络中必须唯一。 子网创建后，无法更改子网名称。 创建虚拟网络时，门户需要定义一个子网，即使虚拟网络不需要包含任何子网。 在创建虚拟网络时，只能在门户中定义一个子网。 虚拟网络创建后，可在将来向虚拟网络添加更多子网。 若要将子网添加到虚拟网络，请参阅[管理子网](virtual-network-manage-subnet.md)。 可以使用 Azure CLI 或 PowerShell 创建具有多个子网的虚拟网络。

       >[!TIP]
       >有时，管理员会创建不同的子网来筛选或控制子网之间的流量路由。 在定义子网之前，请考虑一下需要如何筛选和路由子网之间的流量。 若要详细了解如何筛选子网之间的流量，请参阅[网络安全组](security-overview.md)。 Azure 自动在子网之间路由流量，但你可以替代 Azure 的默认路由。 若要详细了解有关 Azures 默认子网流量路由，请参阅[路由概述](virtual-networks-udr-overview.md)。
       >

     - **子网地址范围**：此范围必须处于为虚拟网络输入的地址空间内。 可以指定的最小范围为 /29，为子网提供八个 IP 地址。 Azure 保留每个子网中的第一个地址和最后一个地址，以确保协议一致性。 此外还会保留三个地址供 Azure 服务使用。 因此，子网地址范围为 /29 的虚拟网络仅有三个可用 IP 地址。 如果打算将虚拟网络连接到 VPN 网关，必须创建一个网关子网。 详细了解[网关子网地址范围具体考虑事项](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 在特定条件下，可以在子网创建后更改地址范围。 若要了解如何更改子网地址范围，请参阅[管理子网](virtual-network-manage-subnet.md)。
     - **订阅**：选择一个[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 不能在多个 Azure 订阅中使用同一个虚拟网络。 但是，可通过[虚拟网络对等](virtual-network-peering-overview.md)将一个订阅中的虚拟网络连接到另一个订阅中的虚拟网络。 任何连接到虚拟网络的 Azure 资源都必须与虚拟网络处于同一订阅中。
     - **资源组**：选择现有的[资源组](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)，或创建一个新组。 连接到虚拟网络的 Azure 资源可以与虚拟网络处于同一资源组中，也可以处于不同资源组中。
     - **位置**：选择 Azure [位置](https://azure.microsoft.com/regions/)（也称为区域）。 一个虚拟网络只能位于一个 Azure 区域中。 但是，可以使用 VPN 网关将一个位置中的虚拟网络连接到另一个位置中的虚拟网络。 任何连接到虚拟网络的 Azure 资源都必须与虚拟网络位于同一区域中。

命令

- Azure CLI: [az network vnet create](/cli/azure/network/vnet)
- PowerShell：[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>查看虚拟网络和设置

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 在虚拟网络列表中，选择要查看其设置的虚拟网络。
3. 将列出所选虚拟网络的以下设置：
   - **概览**：提供有关虚拟网络的信息，包括地址空间和 DNS 服务器。 以下屏幕截图显示了名为 **** MyVNet 的虚拟网络的概述设置：

     ![网络接口概述](./media/manage-virtual-network/vnet-overview.png)

     可以选择“资源组”或“订阅名称”旁边的“更改”，将虚拟网络移动到其他订阅或资源组。**** **** **** 若要了解如何移动虚拟网络，请参阅[将资源移动到不同的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 该文章列出了先决条件，以及如何使用 Azure 门户、PowerShell 和 Azure CLI 移动资源。 所有连接到虚拟网络的资源都必须随着虚拟网络移动。
   - **地址空间**：地址空间：列出分配给虚拟网络的地址空间。 若要了解如何添加和删除地址空间内的地址范围，请完成[添加或删除地址范围](#add-or-remove-an-address-range)中的步骤。
   - **已连接的设备**：列出连接到虚拟网络的所有资源。 在上一个屏幕截图中，有三个网络接口和一个负载均衡器连接到虚拟网络。 将列出已创建并连接到虚拟网络的所有新资源。 如果删除某个已连接到虚拟网络的资源，则它将不再出现在列表中。
   - **子网**：显示存在于虚拟网络内的子网列表。 若要了解如何添加和删除子网，请参阅[管理子网](virtual-network-manage-subnet.md)。
   - **DNS 服务器**：可以指定 Azure 内部 DNS 服务器或自定义 DNS 服务器是否为连接到虚拟网络的设备提供名称解析。 使用 Azure 门户创建虚拟网络时，Azure 的 DNS 服务器默认用于在虚拟网络内进行名称解析。 若要修改 DNS 服务器，请完成本文[更改 DNS 服务器](#change-dns-servers)部分的步骤。
   - **对等互连**：如果订阅中存在现有的对等互连，它们将在此处列出。 可以查看现有对等互连的设置，或者创建、更改或删除对等互连。 若要了解对等互连的详细信息，请参阅[虚拟网络对等互连](virtual-network-peering-overview.md)。
   - **属性**：显示有关虚拟网络的设置，包括虚拟网络的资源 ID 及其所在的 Azure 订阅。
   - **示意图**：该图提供已连接到虚拟网络的所有设备的可视表示形式。 图示提供一些有关设备的关键信息。 若要在此视图中管理设备，请选择设备。
   - **常用 Azure 设置**：若要详细了解常见的 Azure 设置，请参阅以下信息：
     - [活动日志](../azure-monitor/platform/platform-logs-overview.md)
     - [访问控制 (IAM)](../role-based-access-control/overview.md)
     - [标记](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [锁](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [自动化脚本](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

命令

- Azure CLI: [az network vnet show](/cli/azure/network/vnet)
- PowerShell：[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>添加或删除地址范围

可以为虚拟网络添加和删除地址范围。 地址范围必须以 CIDR 表示法指定，并且不能和同一虚拟网络中的其他地址范围重叠。 定义的地址范围可以是公共或专用 (RFC 1918) 地址。 无论是将地址范围定义为公用还是专用，地址范围都只能从虚拟网络内、从互联的虚拟网络以及从任何已连接到虚拟网络的本地网络进行访问。 

可以减小虚拟网络的地址范围，只要它仍包括任何关联子网的范围。 此外，还可以扩展地址范围，例如，将 /16 更改为 /8。 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

无法添加以下的地址范围：

- 224.0.0.0/4（多播）
- 255.255.255.255/32（广播）
- 127.0.0.0/8（环回）
- 169.254.0.0/16（本地链路）
- 168.63.129.16/32（内部 DNS、DHCP 和 Azure 负载均衡器[运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md#probesource)）

添加或删除地址范围：

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 在虚拟网络列表中，单击要为其添加或删除地址范围的虚拟网络。
3. 选择“设置”下的“地址空间”**** ****。
4. 完成以下选项之一：
    - **添加地址范围**：输入新的地址范围。 该地址范围不能与为虚拟网络定义的现有地址范围重叠。
    - **删除地址范围**：在要删除的地址范围右侧，选择“...”，然后选择“删除”**** ****。 如果该地址范围包含子网，则无法删除该地址范围。 要删除某个地址范围，必须先删除存在于该地址范围内的所有子网（以及已连接到这些子网的所有资源）。
5. 选择“保存” 。

命令

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell：[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>更改 DNS 服务器

所有连接到虚拟网络的 VM 都会注册到为虚拟网络指定的 DNS 服务器。 它们还使用指定的 DNS 服务器进行名称解析。 VM 中的每个网络接口 (NIC) 可以有自己的 DNS 服务器设置。 如果 NIC 有自己的 DNS 服务器设置，这些设置会替代虚拟网络的 DNS 服务器设置。 若要了解有关 NIC DNS 设置的详细信息，请参阅[网络接口任务和设置](virtual-network-network-interface.md#change-dns-servers)。 若要详细了解 Azure 云服务中 VM 和角色实例的名称解析，请参阅 [VM 和角色实例的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 添加、更改或删除 DNS 服务器：

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 在虚拟网络列表中，选择要更改其 DNS 服务器的虚拟网络。
3. 选择“设置”下的“DNS 服务器”。**** ****
4. 选择以下选项之一：
   - **默认（Azure 提供）** ：所有资源名称和专用 IP 地址会自动注册到 Azure DNS 服务器。 可以解析连接到同一虚拟网络的任何资源之间的名称。 不能使用此选项跨虚拟网络解析名称。 若要跨虚拟网络解析名称，必须使用自定义 DNS 服务器。
   - **自定义**：可以添加一个或多个服务器，直至达到虚拟网络的 Azure 限制。 若要了解有关 DNS 服务器限制的详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)。 可以使用以下选项：
   - **添加地址**：将服务器添加到虚拟网络 DNS 服务器列表。 此选项还会将 DNS 服务器注册到 Azure。 如果已将 DNS 服务器注册到 Azure，则可以在列表中选择该 DNS 服务器。
   - **删除地址**：在要删除的服务器旁，选择“...”，然后选择“删除”**** ****。 删除服务器只会将服务器从此虚拟网络列表中删除。 DNS 服务器在 Azure 中仍为注册状态，可供其他虚拟网络使用。
   - **重新排列 DNS 服务器的地址**：确认按所处环境的正确顺序列出 DNS 服务器，这一点很重要。 将按指定顺序使用 DNS 服务器列表， 而不是按轮循方式来使用。 如果列表中的第一个 DNS 服务器可以访问，则无论此 DNS 服务器是否正常运行，客户端都会使用该 DNS 服务器。 删除列出的所有 DNS 服务器，然后按照所需顺序，将这些服务器重新添加到列表中。
   - **更改地址**：在列表中突出显示 DNS 服务器，然后输入新地址。
5. 选择“保存” 。
6. 重启已连接到虚拟网络的 VM，以便为其分配新的 DNS 服务器设置。 VM 在重启之前，将继续使用其当前 DNS 设置。

命令

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell：[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>删除虚拟网络

只有在没有资源连接到虚拟网络的情况下，才能删除该虚拟网络。 如果还有资源连接到虚拟网络中的任何子网，则必须首先删除已连接到虚拟网络中所有子网的资源。 删除资源所采取的步骤因资源而异。 若要了解如何删除连接到子网的资源，请阅读要删除的每种资源类型的相关文档。 删除虚拟网络：

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 从虚拟网络列表中，选择要删除的虚拟网络。
3. 在“设置”下单击“已连接的设备”，以确认没有设备连接到该虚拟网络。**** **** 如果有连接的设备，则必须先删除它们，然后才能删除虚拟网络。 如果没有连接的设备，请选择“概述”。****
4. 选择“删除” 。
5. 若要确认删除虚拟网络，请选择“是”****。

命令

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell：[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>权限

若要在虚拟网络上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 操作                                  |   名称                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   读取虚拟网络              |
|Microsoft.Network/virtualNetworks/write  |   创建或更新虚拟网络  |
|Microsoft.Network/virtualNetworks/delete |   删除虚拟网络            |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建虚拟网络
- 为虚拟网络创建和分配 [Azure Policy 定义](policy-samples.md)
