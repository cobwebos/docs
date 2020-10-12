---
title: 向 Azure VM 添加网络接口或从中删除网络接口
description: 了解如何向虚拟机中添加网络接口或从中删除网络接口。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: f7253be2844f40ca52df2f9b3bc9cbba552fea2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480127"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>在虚拟机中添加或删除网络接口。

了解在创建 Azure 虚拟机 (VM) 时如何添加现有的网络接口。 还将了解如何在现有 VM 中添加或删除处于已停止（已解除分配）状态的网络接口。 Azure VM 通过网络接口与 Internet、Azure 及本地资源通信。 VM 有一个或多个网络接口。 

如果需要为网络接口添加、更改或删除 IP 地址，请参阅[管理网络接口 IP 地址](virtual-network-network-interface-addresses.md)。 若要创建、更改或删除网络接口，请参阅[管理网络接口](virtual-network-network-interface.md)。

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果你没有 Azure 帐户，请使用有效的订阅设置一个帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 在开始学习本文的余下内容之前，请完成以下任务之一：

- **门户用户**：使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

- **PowerShell 用户**：运行 [Azure Cloud Shell](https://shell.azure.com/powershell)中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 在 "Azure Cloud Shell 浏览器" 选项卡中，找到 " **选择环境** " 下拉列表，然后选择 " **PowerShell** " （如果尚未选择）。

    如果在本地运行 PowerShell，请使用 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az.Network` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 运行 `Connect-AzAccount`，创建与 Azure 的连接。

- **Azure 命令行接口 (CLI) 用户**：在 [Azure Cloud Shell](https://shell.azure.com/bash)中运行命令，或从计算机运行 cli。 如果在本地运行 Azure CLI，请使用 Azure CLI 2.0.26 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 运行 `az login`，创建与 Azure 的连接。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>将现有网络接口添加到新 VM

通过门户创建虚拟机时，门户会使用默认设置创建一个网络接口，并将其附加到 VM。 无法使用门户将现有网络接口添加到新的 VM，或创建具有多个网络接口的 VM。 可以使用 CLI 或 PowerShell 执行这两项操作。 请确保熟悉[约束](#constraints)。 如果要创建具有多个网络接口的 VM，还必须将操作系统配置为在创建 VM 后正确使用它们。 了解如何将 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 配置为使用多个网络接口。

### <a name="commands"></a>命令

在创建 VM 之前，请[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)。

|工具|命令|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>将网络接口添加到现有 VM

若要向虚拟机添加网络接口，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com)来查找现有虚拟机。 搜索并选择“虚拟机”。

2. 选择你的 VM 的名称。 该 VM 必须支持要添加的网络接口的数量。 若要了解每个 VM 大小支持的网络接口数量，请参阅 Azure 中适用于 [Linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的大小。

3. 在 VM 命令栏中，选择“停止”，然后在确认对话框中选择“确定”。 然后，等待 VM 的“状态”更改为“已停止(已解除分配)” 。

4. 从 VM 菜单栏中，选择“网络” > “附加网络接口”。  然后，在“附加现有网络接口”中，选择要附加的网络接口，并选择“确定”。

    >[!NOTE]
    >选择的网络接口无法启用加速网络，无法获得 IPv6 地址，并且必须位于包含当前附加到 VM 的网络接口的虚拟网络中。

    如果没有现有的网络接口，必须先创建一个。 为此，请选择“创建网络接口”。 若要详细了解如何创建网络接口，请参阅[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)。 若要详细了解将网络接口添加到虚拟机时的约束，请参阅[约束](#constraints)。

5. 从 VM 菜单栏中，选择“概览” > “启动”来重启虚拟机。 

现在，你可以配置 VM 操作系统以正确地使用多个网络接口。 了解如何将 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 配置为使用多个网络接口。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add)（参考）；[详细步骤](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（参考）；[详细步骤](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>查看 VM 的网络接口

可以查看当前附加到 VM 的网络接口，了解每个网络接口的配置，以及分配给每个网络接口的 IP 地址。 

1. 转到 [Azure 门户](https://portal.azure.com)来查找现有虚拟机。 搜索并选择“虚拟机”。

    >[!NOTE]
    >使用分配有订阅“所有者”、“参与者”或“网络参与者”角色的帐户登录。 若要详细了解如何向帐户分配角色，请参阅[针对 Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。

2. 选择你要查看其附加的网络接口的 VM 的名称。

3. 在 VM 菜单栏中，选择“网络”。

若要了解网络接口的设置及其更改方法，请参阅[管理网络接口](virtual-network-network-interface.md)。 若要了解如何添加、更改或删除分配到网络接口的 IP 地址，请参阅[管理网络接口 IP 地址](virtual-network-network-interface-addresses.md)。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>从 VM 中删除网络接口

1. 转到 [Azure 门户](https://portal.azure.com)来查找现有虚拟机。 搜索并选择“虚拟机”。

2. 选择你要查看其附加的网络接口的 VM 的名称。

3. 在 VM 工具栏中，选择“停止”。

4. 等到 VM 的“状态”更改为“已停止(已解除分配)” 。

5. 从 VM 菜单栏中，选择“网络” > “分离网络接口”。 

6. 在“分离网络接口”对话框中，选择要分离的网络接口。 然后选择“确定”。

    >[!NOTE]
    >如果只列出了一个网络接口，则无法将它分离，因为虚拟机上必须始终至少附加一个网络接口。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove)（参考）；[详细步骤](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（参考）；[详细步骤](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>约束

- VM 上必须至少附加一个网络接口。

- VM 上附加的网络接口数量不能超过 VM 大小支持的数量。 若要详细了解每个 VM 大小支持的网络接口数量，请参阅 Azure 中适用于 [Linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的大小。 所有大小至少支持两个网络接口。

- 目前，添加到一个 VM 的网络接口不能附加到另一个 VM。 若要详细了解如何创建网络接口，请参阅[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)。

- 过去，只能向支持多个网络接口且至少是使用两个网络接口创建的 VM 中添加网络接口。 不能向使用一个网络接口创建的 VM 添加网络接口，即使 VM 大小支持多个网络接口。 相反，只能从至少有三个网络接口的 VM 中删除网络接口，因为使用至少两个网络接口创建的 VM 必须始终有至少两个网络接口。 这些约束将不再应用。 现在，可以创建包含任意数量（但不能超过 VM 大小支持的数量）的网络接口的 VM。

- 默认情况下，VM 上附加的第一个网络接口是主网络接口。 VM 中的所有其他网络接口为辅助网络接口。

- 你可以控制要将出站流量发送到的网络接口。 但是，VM 默认情况下会将所有出站流量都发送到分配给主网络接口的主 IP 配置的 IP 地址。

- 过去，同一个可用性集中的所有 VM 都需要有一个或多个网络接口。 现在，同一个可用性集中可以存在具有任意数目的网络接口的 VM，只要 VM 大小支持该数目。 只能在创建 VM 时将其添加到可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。

- 可以将同一 VM 中的网络接口连接到虚拟网络中的不同子网。 但是，网络接口必须全部连接到同一个虚拟网络。

- 可将任何主要或辅助网络接口的任何 IP 配置的任何 IP 地址添加到 Azure 负载均衡器后端池。 过去，只能将主要网络接口的主要 IP 地址添加到后端池。 若要详细了解 IP 地址和配置，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。

- 删除 VM 不会删除附加到它的网络接口。 删除 VM 时，网络接口将与该 VM 分离。 可将那些网络接口添加到不同的 VM，也可将其删除。

- 若要实现所记录的最佳性能，需要使用“加速网络”。 在某些情况下，必须为 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虚拟机显式启用“加速网络”。

## <a name="next-steps"></a>后续步骤

若要创建具有多个网络接口或 IP 地址的 VM，请参阅：

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IPv4 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|创建具有专用 IPv6 地址的单 NIC VM（在 Azure 负载均衡器后）|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure 资源管理器模板](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
