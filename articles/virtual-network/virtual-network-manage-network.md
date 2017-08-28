---
title: "创建、更改或删除 Azure 虚拟网络 | Microsoft Docs"
description: "了解如何在 Azure 中创建、更改或删除虚拟网络。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.contentlocale: zh-cn
ms.lasthandoff: 08/15/2017

---
# <a name="create-change-or-delete-a-virtual-network"></a>创建、更改或删除虚拟网络

了解如何创建和删除虚拟网络以及更改现有虚拟网络的设置，如 DNS 服务器和 IP 地址空间。

虚拟网络是你自己的网络在云中的表示形式。 虚拟网络是对专用于 Azure 订阅的 Azure 云进行的逻辑隔离。 对于所创建的每个虚拟网络，可以：
- 选择要分配的地址空间。 地址空间由通过无类别域际路由选择 (CIDR) 表示法定义的一个或多个地址范围组成，如 10.0.0.0/16。
- 选择使用 Azure 提供的 DNS 服务器，或使用自己的 DNS 服务器。 连接到虚拟网络的所有资源都会分配有此 DNS 服务器，以便解析虚拟网络内的名称。
- 将虚拟网络分段为子网，每个子网在虚拟网络的地址空间内都有其自己的地址范围。 若要了解如何创建、更改和删除子网，请阅读[添加、更改或删除子网](virtual-network-manage-subnet.md)。

本文介绍如何通过使用 Azure 资源管理器部署模型创建、更改和删除虚拟网络。

## <a name="before"></a>准备工作

在开始本文所述的任务之前，请先完成以下先决条件：

- 如果对如何使用虚拟网络不太熟悉，建议查看[创建你的第一个 Azure 虚拟网络](virtual-network-get-started-vnet-subnet.md)中的练习。 此练习有助于熟悉虚拟网络。
- 若要了解有关虚拟网络限制的详细信息，请查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用 Azure 帐户登录到 Azure 门户、Azure 命令行工具 (Azure CLI) 或 Azure PowerShell。 若没有 Azure 帐户，请注册 [免费试用帐户](https://azure.microsoft.com/free)
- 如果打算使用 PowerShell 命令来完成本文中所述的任务，首先必须[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保安装最新版本的 Azure PowerShell cmdlet。 若要获取示例中的 PowerShell 命令的相关帮助，请输入 `get-help <command> -full`。
- 如果打算使用 Azure CLI 命令来完成本文中所述的任务，首先必须[安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保安装最新版本的 Azure CLI。 若要获取 Azure CLI 命令的帮助，请输入 `az <command> --help`。


## <a name="create-vnet"></a>创建虚拟网络

创建虚拟网络：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到[门户](https://portal.azure.com)。 若要详细了解如何将角色和权限分配给帐户，请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 单击“新建” > “网络” > “虚拟网络”。
3. 在“虚拟网络”边栏选项卡上的“选择部署模型”框中，保留选择“资源管理器”，然后单击“创建”。
4. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：
    - 名称：该名称在选择创建虚拟网络的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中必须是唯一的。 虚拟网络创建后，无法更改名称。 可随着时间推移创建多个虚拟网络。 有关命名建议，请参阅[命名约定](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)。 遵循命名约定有助于更加轻松地管理多个虚拟网络。
    - 地址空间：以 CIDR 表示法指定地址空间。 定义的地址空间可以是公共或专用 (RFC 1918) 地址。 无论是将地址空间定义为公用还是专用，地址空间都只能从虚拟网络内、从互联的虚拟网络以及从任何已连接到虚拟网络的本地网络进行访问。 无法添加以下的地址空间：
        - 224.0.0.0/4（多播）
        - 255.255.255.255/32（广播）
        - 127.0.0.0/8（环回）
        - 169.254.0.0/16（本地链路）
        - 168.63.129.16/32（内部 DNS）

      尽管在创建虚拟网络时只能定义一个地址空间，但可以在虚拟网络创建之后添加更多地址空间。 若要了解如何将地址空间添加到现有的虚拟网络，请参阅本文中的[添加或删除地址空间](#add-address-spaces)。

      >[!WARNING]
      >如果虚拟网络的地址空间与另一个虚拟网络或本地网络重叠，那么这两个网络都不能连接。 定义地址空间之前，请考虑将来是否会想要将此虚拟网络连接到其他虚拟网络或本地网络。
      >
      >

    - 子网名称：子网名称在虚拟网络中必须是唯一的。 子网创建后，无法更改子网名称。 创建虚拟网络时，门户需要定义一个子网，即使虚拟网络不需要包含任何子网。 在创建虚拟网络时，只能在门户中定义一个子网。 虚拟网络创建后，可随后向虚拟网络添加更多子网。 若要将子网添加到虚拟网络，请参阅[创建、更改或删除子网](virtual-network-manage-subnet.md)中的[创建子网](virtual-network-manage-subnet.md#create-subnet)。 可以使用 Azure CLI 或 PowerShell 创建具有多个子网的虚拟网络。

      >[!TIP]
      >有时，管理员会创建不同的子网来筛选或控制子网之间的流量路由。 在定义子网之前，请考虑如何筛选和路由子网之间的流量。 若要详细了解如何筛选子网之间的流量，请参阅[网络安全组](virtual-networks-nsg.md)。 Azure 自动在子网之间路由流量，但你可以替代 Azure 的默认路由。 若要了解如何替代 Azure 默认子网流量路由，请参阅[用户定义的路由](virtual-networks-udr-overview.md)。
      >

    - 子网地址范围：此范围必须处于为虚拟网络输入的地址空间内。 可以指定的最小范围为 /29，为子网提供八个 IP 地址。 Azure 保留每个子网中的第一个地址和最后一个地址，以确保协议一致性。 此外还会保留三个地址供 Azure 服务使用。 因此，子网地址范围为 /29 的虚拟网络仅有三个可用 IP 地址。 如果打算将虚拟网络连接到 VPN 网关，必须创建一个网关子网。 详细了解[网关子网地址范围具体考虑事项](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 在特定条件下，可以在子网创建后更改地址范围。 若要了解如何更改子网地址范围，请参阅[添加、更改或删除子网](virtual-network-manage-subnet.md)中的[更改子网设置](#change-subnet)。
    - 订阅：选择[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 不能在多个 Azure 订阅中使用同一个虚拟网络。 但是，可以将一个订阅中的虚拟网络连接到另一个订阅中的虚拟网络。 要连接不同订阅中的虚拟网络，请使用 Azure VPN 网关或虚拟网络对等互连。 任何连接到虚拟网络的 Azure 资源都必须与虚拟网络处于同一订阅中。
    - 资源组：选择现有[资源组](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)或创建新的资源组。 连接到虚拟网络的 Azure 资源可以与虚拟网络处于同一资源组中，也可处于不同资源组中。
    - 位置：选择 Azure [位置](https://azure.microsoft.com/regions/)（也称为区域）。 虚拟网络只能位于一个 Azure 区域中。 但是，可以使用 VPN 网关将一个位置中的虚拟网络连接到另一个位置中的虚拟网络。 任何连接到虚拟网络的 Azure 资源都必须与虚拟网络位于同一区域中。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>查看虚拟网络和设置

查看虚拟网络和设置：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到[门户](https://portal.azure.com)。 若要详细了解有关如何将角色和权限分配给帐户，请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在门户的搜索框中，输入“虚拟网络”。 在搜索结果中，单击“虚拟网络”。
3. 在“虚拟网络”边栏选项卡中，单击要查看其设置的虚拟网络。
4. 针对所选虚拟网络显示的边栏选项卡中将列出以下设置：
    - 概述：提供有关虚拟网络的信息，包括地址空间和 DNS 服务器。 以下屏幕截图显示了名为 **MyVNet** 的虚拟网络的概述设置：

        ![网络接口概述](./media/virtual-network-manage-network/vnet-overview.png)

      在“概述”边栏选项卡中，可以将虚拟网络移动到不同的订阅或资源组。 若要了解如何移动虚拟网络，请参阅[将资源移动不同的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 该文章列出了先决条件，以及如何使用 Azure 门户、PowerShell 和 Azure CLI 移动资源。 所有连接到虚拟网络的资源都必须随着虚拟网络移动。
    - 地址空间：列出分配给虚拟网络的地址空间。 若要了解如何添加和删除地址空间，请完成本文的[添加或删除地址空间](#address-spaces)中所述的步骤。
    - 已连接的设备：列出连接到虚拟网络的所有资源。 在上一个屏幕截图中，有三个网络接口和一个负载均衡器连接到虚拟网络。 将列出创建并连接到虚拟网络的所有新资源。 如果删除某个已连接到虚拟网络的资源，则它将不再出现在列表中。
    - 子网：显示存在于虚拟网络内的子网列表。 若要了解如何添加和删除子网，请参阅[添加、更改或删除子网](virtual-network-manage-subnet.md)中的[创建子网](virtual-network-manage-subnet.md#create-subnet)和[删除子网](virtual-network-manage-subnet.md#delete-subnet)。
    - DNS 服务器：可以指定 Azure 内部 DNS 服务器或自定义 DNS 服务器是否为连接到虚拟网络的设备提供名称解析。 使用 Azure 门户创建虚拟网络时，Azure 的 DNS 服务器默认用于在虚拟网络内进行名称解析。 若要修改 DNS 服务器，请完成本文的[添加、更改和删除 DNS 服务器](#dns-servers)中所述的步骤。
    - 对等互连：如果订阅中存在现有的对等互连，它们将在此处列出。 可以查看现有对等互连的设置，或者创建、更改或删除对等互连。 若要了解对等互连的详细信息，请参阅[虚拟网络对等互连](virtual-network-peering-overview.md)。
    - 属性：显示有关虚拟网络的设置，包括虚拟网络的资源 ID 及其所在的 Azure 订阅。
    - 图示：该图提供已连接到虚拟网络的所有设备的可视表示形式。 该图具有设备相关的一些关键信息。 若要在此视图中管理设备，请单击设备。
    - 常见 Azure 设置：若要详细了解常见的 Azure 设置，请参阅以下信息：
        *   [活动日志](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [访问控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [标记](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [锁](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [自动化脚本](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>添加或删除地址空间

可以为虚拟网络添加和删除地址空间。 地址空间必须以 CIDR 表示法指定，并且不能和同一虚拟网络中的其他地址空间重叠。 定义的地址空间可以是公共或专用 (RFC 1918) 地址。 无论是将地址空间定义为公用还是专用，地址空间都只能从虚拟网络内、从互联的虚拟网络以及从任何已连接到虚拟网络的本地网络进行访问。 无法添加以下的地址空间：

- 224.0.0.0/4（多播）
- 255.255.255.255/32（广播）
- 127.0.0.0/8（环回）
- 169.254.0.0/16（本地链路）
- 168.63.129.16/32（内部 DNS）

添加或删除地址空间：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到[门户](https://portal.azure.com)。 若要详细了解有关如何将角色和权限分配给帐户，请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在门户的搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。
3. 在“虚拟网络”边栏选项卡中，单击想要为其添加或删除地址空间的虚拟网络。
4. 在虚拟网络边栏选项卡中的“设置”下，单击“地址空间”。
5. 在地址空间的边栏选项卡中，完成以下选项之一：
    - 添加地址空间：输入新的地址空间。 该地址空间不能与为虚拟空间定义的现有地址空间重叠。
    - 删除地址空间：右键单击某个地址空间，然后单击“删除”。 如果该地址空间包含子网，则无法删除该地址空间。 要删除某个地址空间，必须先删除存在于该地址空间中的所有子网（以及已连接到这些子网的所有资源）。
6. 单击“保存”。

**命令**

|工具|命令|
|---|---|
|Azure CLI|仅限资源管理器|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>添加、更改或删除 DNS 服务器

所有连接到虚拟网络的 VM 都会注册为虚拟网络指定的 DNS 服务器。 它们还使用指定的 DNS 服务器进行名称解析。 VM 中的每个网络接口 (NIC) 可以有自己的 DNS 服务器设置。 如果 NIC 有自己的 DNS 服务器设置，它们将替代虚拟网络的 DNS 服务器设置。 若要了解有关 NIC DNS 设置的详细信息，请参阅[网络接口任务和设置](virtual-network-network-interface.md#change-dns-servers)。 若要详细了解 Azure 云服务中 VM 和角色实例的名称解析，请参阅 [VM 和角色实例的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 添加、更改或删除 DNS 服务器：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到[门户](https://portal.azure.com)。 若要详细了解如何将角色和权限分配给帐户，请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在门户的搜索框中，键入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。
3. 在“虚拟网络”边栏选项卡中，单击想要为其更改 DNS 设置的虚拟网络。
4. 在虚拟网络边栏选项卡中的“设置”下，单击“DNS 服务器”。
5. 在列出 DNS 服务器的边栏选项卡中选择以下选项之一：
    - **默认(Azure 提供)**：所有资源名称和专用 IP 地址将自动注册到 Azure DNS 服务器。 可以解析连接到同一虚拟网络的任何资源之间的名称。 不能使用此选项跨虚拟网络解析名称。 若要跨虚拟网络解析名称，必须使用自定义 DNS 服务器。
    - 自定义：可以添加一个或多个服务器，直至达到虚拟网络的 Azure 限制。 要了解有关 DNS 服务器限制的详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)。 可以使用以下选项：
        - 添加地址：将服务器添加到虚拟网络 DNS 服务器列表。 此选项还会向 Azure 注册 DNS 服务器。 如果已将 DNS 服务器注册到 Azure，则可以在列表中选择该 DNS 服务器。
        - 删除地址：在要删除的服务器旁边，单击 **X**。删除服务器只会将服务器从此虚拟网络列表中删除。 DNS 服务器在 Azure 中仍为注册状态，可供其他虚拟网络使用。
        - **重新排列 DNS 服务器的地址**：确认按所处环境的正确顺序列出 DNS 服务器，这一点很重要。 将按指定服务器的顺序使用 DNS 服务器列表。 它们并非以轮循机制工作。 如果列表中的第一个 DNS 服务器可访问，则无论此 DNS 服务器是否运行正常，客户端都会使用该 DNS 服务器。 删除列出的所有 DNS 服务器，并按照所需顺序，将这些服务器重新添加到列表中。
        - 更改地址：在列表中突出显示 DNS 服务器，然后输入新名称。
6. 单击“保存”。
7. 重启已连接到虚拟网络的 VM，以便为它们分配新的 DNS 服务器设置。 VM 在重新启动之前，将继续使用其当前 DNS 设置。

**命令**

|工具|命令|
|---|---|
|Azure CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>删除虚拟网络

只有在没有资源连接到虚拟网络的情况下，才能删除该虚拟网络。 如果还有资源连接到虚拟网络中的任何子网，则必须首先删除已连接到虚拟网络中所有子网的资源。 删除资源所采取的步骤因资源而异。 若要了解如何删除已连接到子网的资源，请阅读针对要删除的每种资源类型的相关文档。 删除虚拟网络：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到[门户](https://portal.azure.com)。 若要详细了解如何将角色和权限分配给帐户，请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在门户的搜索框中，输入“虚拟网络”。 在搜索结果中，单击“虚拟网络”。
3. 在“虚拟网络”边栏选项卡中，选择想要删除的虚拟网络。
4. 在虚拟网络的边栏选项卡中，要确认没有设备连接到虚拟网络，请在“设置”下单击“已连接的设备”。 如果有连接的设备，则必须先删除它们，然后才能删除虚拟网络。 如果没有连接的设备，请单击“概述”。
5. 在边栏选项卡顶部单击“删除”。
6. 若要确认删除虚拟网络，请单击“是”。


**命令**

|工具|命令|
|---|---|
|Azure CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>后续步骤

- 若要创建 VM 然后将其连接到虚拟网络，请参阅[创建虚拟网络并连接 VM](virtual-network-get-started-vnet-subnet.md#create-vms)。
- 若要筛选虚拟网络内子网之间的网络流量，请参阅[创建网络安全组](virtual-networks-create-nsg-arm-pportal.md)。
- 若要将虚拟网络对等互连到另一个虚拟网络，请参阅[创建虚拟网络对等互连](virtual-network-create-peering.md#portal)。
- 若要了解用于将虚拟网络连接到本地网络的选项，请参阅[关于 VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)。

