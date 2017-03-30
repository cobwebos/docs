---
title: "Azure 网络接口 | Microsoft Docs"
description: "了解如何创建和删除网络接口 (NIC)，以及将公共和专用 IP 地址分配给 NIC。 了解如何在 Azure 虚拟机上附加和分离 NIC。"
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c3e5ad8b7c8325049cb53d709673c7c7ad58108f
ms.lasthandoff: 03/22/2017


---

# <a name="network-interfaces"></a>网络接口

了解网络接口 (NIC) 及其用法。 NIC 是 Azure 虚拟机 (VM) 与基础软件网络之间互相连接的桥梁。 下图演示了 NIC 提供的功能：

![网络接口](./media/virtual-network-network-interface/nic.png)

本文介绍如何使用图中所示的概念。 单击以下任一概念可直接转到本文的相关部分。

- [网络接口](#nics)：NIC 连接到 Azure 虚拟网络 (VNet) 中的一个子网。 在图中，**VM1** 上附加了两个 NIC，**VM2** 上附加了一个 NIC。 每个 NIC 连接到相同的 VNet 但不同的子网。 此部分提供列出现有 NIC 以及创建、更改和删除 NIC 的步骤。 
- [IP 配置](#ip-configs)：每个 NIC 与一个或多个 IP 配置相关联。 每个 IP 配置中分配了专用 IP 地址。 IP 配置可与公共 IP 地址相关联。 在图中，**NIC1** 和 **NIC3** 各有一个关联的 IP 配置，而 **NIC2** 有两个关联的 IP 配置。 分配到 NIC1 和 NIC3 的 IP 配置被分配公共 IP 地址，而分配到 NIC2 的两个 IP 配置都没有被分配公共 IP 地址。 此部分提供使用静态和动态分配方法来创建、更改和删除分配有专用 IP 地址的 IP 配置的步骤。 本部分还提供将公共 IP 地址与 IP 配置相关联以及取消关联的步骤。 
- [网络安全组](#nsgs)：网络安全组 (NSG) 包含一个或多个入站或出站安全规则。 这些规则控制可流入和流出网络接口和/或子网的网络流量类型。 在图中，**NIC1** 和 **NIC3** 具有关联的 NSG，而 **NIC2** 则没有。 此部分提供查看应用到 NIC 的 NSG、将 NSG 添加到 NIC 以及从 NIC 中删除 NSG 的步骤。
- [虚拟机](#vms)：VM 上至少附加了一个 NIC，但根据 VM 大小，有时可以附加多个 NIC。 若要查看每个 VM 大小支持的 NIC 数目，请参阅有关 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) 或 [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) VM 大小的文章。 此部分提供创建单 NIC 和多 NIC VM，以及在现有 VM 上附加和分离 NIC 的步骤。

如果你不太熟悉 Azure 中的 NIC 和 Vm，我们建议在阅读本文之前，先完成[创建第一个 Azure 虚拟网络](virtual-network-get-started-vnet-subnet.md)中的练习。 该练习可帮助你熟悉 VNet 和 VM。 

本文适用于通过 Azure Resource Manager 部署模型创建的 VM 和 NIC。 Microsoft 建议通过 Resource Manager 部署模型而不是经典部署模型创建资源。 如果你不熟悉这两个模型之间的差别，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

本文的剩余部分提供完成所有 NIC 相关任务的步骤。 每个部分列出了：
- 在 Azure 门户中完成任务的步骤。 若要完成这些步骤，必须登录到 [Azure 门户](http://portal.azure.com)。 如果你没有帐户，可注册一个[免费试用帐户](https://azure.microsoft.com/free)。
- 用于通过 Azure PowerShell 完成任务的命令，以及相关命令的命令参考链接。 请完成[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 PowerShell。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 用于通过 Azure 命令行接口 (CLI) 完成任务的命令，以及相关命令的命令参考链接。 请完成[如何安装和配置 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> -h`。

## <a name="nics"></a>网络接口
完成以下部分中的步骤来创建、查看、更改和删除网络接口与设置：

### <a name="create-nic"></a>创建网络接口

NIC 可以附加到某个 VM，也可以保留在自身的 VM 上。 若要了解如何将 NIC 附加到 VM，请参阅本文的[将 NIC 附加到虚拟机](#vm-attach-nic)部分。

若要创建 NIC，请完成以下步骤：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅 [用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在出现的“网络接口”边栏选项卡中，单击“+ 添加”。
4. 在出现的“创建网络接口”边栏选项卡中，输入或选择以下设置的值，然后单击“创建”：
    
    |**设置**|**必需？**|**详细信息**|
    |---|---|---|
    |**Name**|是|创建 NIC 后无法更改其名称。 该名称在所选的资源组中必须唯一。 有关命名建议，请参阅[命名约定](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文。|
    |**虚拟网络**|是|只能将 NIC 连接到与 NIC 位于相同位置和订阅中的 VNet。 NIC 附加到的 VM 也必须位于该 NIC 所在的同一位置和订阅中。 如果未列出任何 VNet，则需要创建一个。 若要创建 VNet，请完成[虚拟网络](virtual-networks-create-vnet-arm-pportal.md)一文中的步骤。 创建 NIC 后，无法更改它连接到的 VNet。|
    |**子网**|是|在所选的 VNet 中选择一个子网。 创建 NIC 后，可以更改它连接到的子网。|
    |**专用 IP 地址分配**|是| 创建 NIC 时，Azure DHCP 服务器会向它分配专用 IP 地址。 DHCP 服务器将从针对 NIC 所连接到的子网定义的子网地址范围中分配一个可用地址。 **动态：**当 NIC 附加到的 VM 在处于停止（解除分配）状态后启动时，Azure 会向 NIC 分配一个不同的地址。 如果在 VM 不是处于停止（解除分配）的状态下将它重新启动，地址将保持不变。 **静态：**静态地址不会更改，除非手动将它更改或者删除 NIC。 创建 NIC 后，可以更改分配方法。|
    |**网络安全组**|否|使用网络安全组可以控制传入和传出 NIC 的网络流量。 有关 NSG 的详细信息，请参阅[网络安全组](virtual-networks-nsg.md)一文。 可将零个或一个网络安全组 (NSG) 应用到 NIC。 也可以将零个或一个 NSG 应用到 NIC 所连接到的子网。 将 NSG 应用到 NIC 及其连接到的子网时，有时会发生意外的结果。 若要对应用到 NIC 的 NSG 进行故障排除，请参阅 [NSG 故障排除](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。|
    |**订阅**|是| 将 NIC 附加到的 VM 以及将 NIC 连接的 VNet 必须在同一个订阅中。|
    |**资源组**|是| NIC 可与它附加的 VM 或者连接到的 VNet 位于相同或不同的资源组中。|
    |**位置**|是|将 NIC 附加到的 VM 以及将 NIC 连接的 VNet 必须位于同一个位置。|

Azure 门户将创建使用专用 IP 地址的、名为 **ipconfig1** 的主 IP 配置，并将该配置关联到所创建的 NIC。 若要了解有关 IP 配置的详细信息，请参阅本文的 [IP 配置](#ip-configs)部分。 创建 NIC 时，无法指定门户创建的 IP 配置名称、分配静态专用 IP 地址或分配公共 IP 地址。 如果使用 PowerShell 或 CLI 创建 NIC，可以指定 IP 配置名称、静态 IP 地址和分配公共 IP 地址。 创建 NIC 后，可以更改专用 IP 地址分配方法，以及确定是否要将公共 IP 地址关联到 NIC。 若要在创建 NIC 后更改设置，请完成本文[更改 IP 配置](#change-ip-config)部分中的步骤。 

>[!Note]
> 只有在 NIC 附加到 VM 后首次启动 VM 时，Azure 才将 MAC 地址分配给 NIC。 你无法自行指定 Azure 要分配给 NIC 的 MAC 地址。 除非 NIC 被删除或者分配给主 NIC 的主 IP 配置的专用 IP 地址发生更改，否则该 MAC 地址将一直分配给该 NIC。 若要了解有关 IP 配置的详细信息，请参阅本文的 [IP 配置](#ip-configs)部分。

|**工具**|**命令**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>查看和更改网络接口与设置

若要查看和更改网络接口与设置，请完成以下步骤：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在“网络接口”边栏选项卡中，单击想要查看或更改其设置的 NIC。
4. 针对所选 NIC 显示的边栏选项卡中将列出以下设置：
    - **概述：**提供有关 NIC 的信息，例如，分配给该 NIC 的 IP 地址、该 NIC 连接到的 VNet/子网，以及该 NIC 附加到的 VM（如果已附加到 VM）。下图显示名为 **mywebserver256** 的 NIC 的概述设置：  ![网络接口概述](./media/virtual-network-network-interface/nic-overview.png)
    - **IP 配置：**NIC 中至少分配了一个 IP 配置，但也可以向它分配多个 IP 配置。 若要了解 NIC 支持的 IP 配置数上限，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 每个 IP 配置有一个分配的专用 IP 地址，可与一个公共 IP 地址关联。 若要修改显示的内容，请完成本文[向 NIC 分配辅助 IP 配置](#create-ip-config)、[更改 IP 配置](#change-ip-config)或[删除 IP 配置](#delete-ip-config)部分中的步骤。
    - **DNS 服务器：**可以指定 Azure DHCP 服务器要向哪个 DNS 服务器分配 NIC。 可在 Azure 内部 DNS 服务器和自定义 DNS 服务器之间选择。 若要修改显示的内容，请完成本文[更改 NIC 的 DNS 设置](#dns)部分中的步骤。
    - **网络安全组 (NSG)：**显示 NIC 是否有关联的 NSG。 如果 NIC 有关联的 NSG，将显示关联的 NSG 的名称。 若要修改显示的内容，请完成本文[在网络接口中关联或取消关联 NSG](#associate-nsg) 部分中的步骤。
    - **属性：**显示 NIC 的关键设置，包括它的 MAC 地址以及所在的订阅。 可将 NIC 移到不同的资源组或订阅，但必须同时移动与该 NIC 相关的所有资源。 例如，如果 NIC 已附加到 VM，则还必须移动该 VM 及其相关的所有其他资源。 若要移动 NIC，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)一文。 此文列出了先决条件，以及如何使用 Azure 门户、PowerShell 和 Azure CLI 移动资源。
    - **有效的安全规则：**如果 NIC 已附加到正在运行的 VM，并且某个 NSG 规则已关联到该 NIC 和/或它连接到的子网，则会列出安全规则。 若要详细了解显示的内容，请参阅[网络安全组故障排除](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。 有关 NSG 的详细信息，请参阅[网络安全组](virtual-networks-nsg.md)一文。
    - **有效的路由：**如果 NIC 已附加到正在运行的 VM，则会列出路由。 路由是 Azure 默认路由、用户定义的任何路由和 NIC 所连接到的子网可能存在的任何 BGP 路由的组合。 若要详细了解显示的内容，请参阅[路由故障排除](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)一文。 若要详细了解用户定义的路由，请阅读[用户定义的路由](virtual-networks-udr-overview.md)一文。
    - **常见的 Azure Resource Manager 设置：**若要详细了解常见的 Azure Resource Manager 设置，请参阅文章[活动日志](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[访问控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[标记](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[锁定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自动化脚本](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)。

|**工具**|**命令**|
|---|---|
|**CLI**|使用 [az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 可查看订阅中的 NIC；使用 [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 可查看 NIC 的设置|
|**PowerShell**|使用 [Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) 可查看订阅中的 NIC，或 NIC 的设置|

### <a name="dns"></a>更改 NIC 的 DNS 设置

若要更改 NIC 的 DNS 设置，请完成以下步骤。 Azure DHCP 服务器将 DNS 服务器分配到 VM。 若要详细了解 NIC 的名称解析设置，请参阅 [VM 的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

1. 对于想要更改其设置的 NIC，请完成本文[查看和更改网络接口与设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“DNS 服务器”。
3. 单击以下选项之一：
    - **从虚拟网络继承(默认)**：选择此选项将会继承针对 NIC 所连接到的虚拟网络定义的 DNS 服务器设置。 系统将在 VNet 级别定义一个自定义 DNS 服务器或 Azure 提供的 DNS 服务器。 Azure 提供的 DNS 服务器可以解析连接到相同 VNet 的资源的名称，但无法解析连接到不同 VNet 的资源的名称。
    - **自定义**：你可以配置自己的 DNS 服务器来解析多个 VNet 中的名称。 输入要用作 DNS 服务器的服务器的 IP 地址。 指定的 DNS 服务器地址只会分配到此 NIC，将覆盖 NIC 连接到的任何 VNet 的 DNS 设置。
4. 单击“保存” 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>更改 NIC 的 IP 转发

IP 转发使 NIC 附加到的 VM 能够：
- 接收不是发往分配给 NIC 的任一 IP 配置具有 IP 地址的网络流量。
- 使用与分配给 NIC 某个 IP 配置的源 IP 地址不同的地址发送网络流量。

必须为附加到 VM 的、接收 VM 需要转发的流量的每个 NIC 启用该设置。 不管 VM 上附加了一个还是多个 NIC，该 VM 都可转发流量。 尽管 IP 转发是一项 Azure 设置，但 VM 也必须运行某个应用程序（例如防火墙、WAN 优化和负载均衡应用程序）才能转发流量。 运行网络应用程序的 VM 通常称为网络虚拟设备 (NVA)。 可在 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)中查看随时可部署的 NVA 列表。 IP 转发通常与用户定义的路由配合使用。 若要详细了解用户定义的路由，请阅读[用户定义的路由](virtual-networks-udr-overview.md)一文。 

若要更改 NIC 的 IP 转发设置，请完成以下步骤：

1. 对于想要修改的 NIC，请完成本文[查看和更改网络接口与设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“IP 配置”
3. 单击“已启用”或“禁用”（默认设置）来更改设置。
4. 单击“保存” 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>更改 NIC 连接到的子网

可以更改 NIC 连接到的子网，但无法更改它连接到的 VNet。 若要更改子网，必须为该 NIC 关联的 IP 配置分配动态专用 IP 地址。 若要更改 NIC 连接到的子网，请完成以下步骤：

1. 对于想要连接到其他子网的 NIC，请完成本文[查看和更改网络接口与设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“IP 配置”。 如果所列的任何 IP 配置的任何专用 IP 地址包含使用静态方法分配的专用 IP 地址，必须通过完成以下步骤将方法更改为动态。 如果使用动态方法分配地址，请转到步骤 3：
    - 在 IP 配置列表中，单击想要更改的 IP 配置的静态 IP 地址。
    - 在显示的 IP 配置边栏选项卡中，选择“动态”作为“分配”方法。
    - 单击“保存” 。
3. 从“子网”下拉列表中选择要将 NIC 连接到的子网。
4. 单击“保存” 。 新的动态地址是从网络子网地址范围中分配的。 如果需要，随后可分配新的子网地址范围中的静态 IP 地址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>删除网络接口

只要 NIC 未附加到 VM，就可以删除该 NIC。 如果它已附加到 VM，则必须先从 VM 中将它分离，然后才能删除它。 若要从 VM 中分离 NIC，请完成本文[从虚拟机中分离 NIC](#vm-detach-nic) 部分中的步骤。

1. 对于想要删除的 NIC，请完成本文[查看和更改网络接口与设置](#view-nics)部分中的步骤 1-2。
2. 右键单击要删除的 NIC，然后单击“删除”。
3. 单击“是”确认删除该 NIC。

删除 NIC 时，将会释放已分配给它的所有 MAC 或 IP 地址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>IP 配置
每个 NIC 至少有一个 IP 配置，称为**主**配置。 NIC 还可有一个或多个关联的*辅助* IP 配置。 可分配到 NIC 的 IP 地址数有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 每个 IP 配置：
- 有一个使用静态或动态分配方法分配的专用 IP 地址。 如果在 VM 处于停止（解除分配）状态后将它启动，动态 IP 地址可能会更改。 只有在删除 NIC 后，才会从 NIC 中释放静态 IP 地址。
- 可与一个公共 IP 地址相关联。

Azure DHCP 服务器将 NIC 主 IP 配置的专用 IP 地址分配到 VM 操作系统中的 NIC。

在如下所述的场景中，向 NIC 分配多个 IP 地址会很有帮助：
- 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
- VM 充当网络虚拟设备，例如防火墙或负载均衡器。
- 可将任何 NIC 的任何专用 IP 地址添加到 Azure 负载均衡器后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要详细了解如何对多个 IP 配置进行负载均衡，请阅读[对多个 IP 配置进行负载均衡](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

可在订阅中使用的公共 IP 地址数有限制，可分配到 NIC 的专用 IP 地址数也有限制。 若要详细了解这些限制，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

### <a name="create-ip-config"></a>将辅助 IP 配置添加到 NIC

可将任意数量的 IP 配置添加到 NIC，只要不超过 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文中所列的限制即可。 若要将 IP 配置添加到 NIC，请完成以下步骤：
 
1. 对于想要将 IP 配置添加到的 NIC，请完成本文[查看网络接口设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“IP 配置”。
3. 在打开的 IP 配置边栏选项卡中单击“+ 添加”。
4. 指定以下设置，然后单击“确定”关闭“添加 IP 配置”边栏选项卡：

    |**设置**|**必需？**|**详细信息**|
    |---|---|---|
    |**Name**|是|必须是唯一的 NIC 名称|
    |**类型**|是|由于你要将 IP 配置添加到现有 NIC，而每个 NIC 都必须有一个主 IP 配置，因此，唯一的选项是“辅助”。|
    |**专用 IP 地址分配方法**|是|如果在 VM 处于停止（解除分配）的状态下将它重新启动，**动态**地址可能会更改。 只有在删除 NIC 之后，**静态**地址才会释放。 从当前未由其他 IP 配置使用的子网地址空间范围中指定一个 IP 地址。|
    |公共 IP 地址|否|**已禁用：**该 IP 配置当前没有关联的公共 IP 地址资源。 **已启用：**选择现有的公共 IP 地址，或新建一个。 若要了解如何创建公共 IP 地址，请参阅[公共 IP 地址](virtual-network-public-ip-address.md#create)一文。|
5. 可以遵循[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的说明，手动将辅助专用 IP 地址添加到 VM 操作系统。 请不要将任何公共 IP 地址添加到 VM 操作系统。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>更改 IP 配置

若要更改任何主要或辅助 IP 配置的专用和公用 IP 地址设置，请完成以下步骤：

1. 对于想要修改的 NIC，请完成本文[查看网络接口设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“IP 配置”。
3. 在打开的 IP 配置边栏选项卡上显示的列表中，单击要修改的 IP 配置。
4. 参考本文[添加 IP 配置](#create-ip-config)部分中有关设置的信息，根据需要更改设置，然后单击“保存”关闭所选 IP 配置的边栏选项卡。

>[!NOTE]
>如果主 NIC 有多个 IP 配置，而你更改了主 IP 配置的专用 IP 地址，则必须手动将所有辅助 IP 地址重新分配到 Windows 中的 NIC（在 Linux 中不需要执行此操作）。 若要手动将 IP 地址分配到操作系统中的 NIC，请参阅[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 请不要将任何公共 IP 地址添加到 VM 操作系统。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>从 NIC 中删除辅助 IP 配置

完成以下步骤可从 NIC 中删除辅助 IP 配置：

1. 对于想要修改的 NIC，请完成本文[查看网络接口设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“IP 配置”。
3. 右键单击想要删除的辅助 IP 配置，然后单击“删除”。 如果该配置具有关联的公共 IP 地址资源，将从 IP 配置中取消关联该资源，但不会删除该资源。
4. 关闭“IP 配置”边栏选项卡。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>网络安全组
网络安全组 (NSG) 包含入站和出站规则的列表，这些规则可以允许或拒绝发往 NIC 的网络流量。 NIC 及其连接到的子网不需要有关联的 NSG。 不过，可将 NSG 关联到 NIC 和/或 NIC 连接到的子网。 包含适用于与子网连接的所有 NIC 的 NSG 通常与子网关联。 这样，就可以向单个 NIC 应用包含更精细规则的 NSG。 有关网络安全组的详细信息，请参阅[网络安全组](virtual-networks-nsg.md)一文。

### <a name="associate-nsg"></a>在网络接口中关联或取消关联 NSG

若要在 NIC 中关联或取消关联 NSG，请完成以下步骤：

1. 对于想要在其中关联或取消关联 NSG 的NIC，请完成本文[查看和更改网络接口与设置](#view-nics)部分中的步骤 1-3。
2. 在所选 NIC 的边栏选项卡中，单击“网络安全组”。 此时将出现一个边栏选项卡，其顶部显示了“编辑”。 如果 NIC 当前没有任何关联的 NSG，将显示“网络安全组: 无”显示。 如果 NIC 当前有关联的 NSG，将显示“网络安全组: NSG-Name”（其中，NSG-Name 是当前关联到 NIC 的 NSG 名称）。
3. 单击“编辑”。
4. 单击“网络安全组”。 如果未列出任何网络安全组，原因是订阅中没有任何网络安全组。 若要创建 NSG，请完成[网络安全组](virtual-networks-create-nsg-arm-pportal.md)一文中的步骤。 
5. 在出现的“选择网络安全组”边栏选项卡中的列表内，单击某个现有的 NSG 将该 NSG 关联到 NIC，或单击“无”取消关联当前与 NIC 关联的 NSG。
6. 单击“保存” 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)]|

## <a name="vms"></a>在虚拟机中附加和分离 NIC

可以在创建 VM 时向它附加现有的 NIC，也可以将现有的 NIC 附加到现有的 VM。 还可以从至少有两个 NIC 的现有 VM 中分离 NIC。 尽管在你创建 VM 时门户会创建 NIC，但在门户中无法执行以下操作：

- 创建 VM 时指定要附加的现有 NIC
- 创建附有多个 NIC 的 VM
- 指定 NIC 的名称（门户将使用默认名称创建 NIC）
- 将专用 IP 地址分配方法指定为静态。 门户会自动分配动态专用 IP 地址，不过，在门户创建 NIC 后，你可以更改分配方法。

使用 PowerShell 或 CLI 可以创建具有上述所有属性的 NIC 或 VM，而使用门户却做不到这一点。 在完成以下部分中的任务之前，请考虑以下约束和行为：

- VM 大小必须支持多个 NIC。 若要了解每个 VM 大小支持的 NIC 数目，请参阅有关 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) 或 [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) VM 大小的文章。
- 默认情况下，附加到 VM 的第一个 NIC 定义为*主* NIC。 附加到 VM 的其他所有 NIC 为*辅助* NIC。
- 默认情况下，来自 VM 的所有出站流量是通过分配给主 NIC 主 IP 配置的 IP 地址发出的。 当然，你可以在 VM 的操作系统中控制要将哪个 IP 地址用于出站流量。
- 过去，同一个可用性集中的所有 VM 都需要有一个或多个 NIC。 现在，同一个可用性集中可以包含具有任意数量的 NIC 的 VM。 不过，只能在创建 VM 时将 VM 添加到可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)一文。
- 尽管附加到同一个 VM 的 NIC 可以连接到 VNet 中的不同子网，但这些 NIC 必须全部连接到同一个 VNet。
- 可将任何主要或辅助 NIC 的任何 IP 配置的任何 IP 地址添加到 Azure 负载均衡器后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。

### <a name="vm-create"></a>创建虚拟机时附加一个或多个 NIC

无法使用 Azure 门户将现有 NIC 附加到新的 VM，或创建具有多个 NIC 的 VM。 创建 VM 时，可使用以下 Azure CLI 或 PowerShell 命令附加一个或多个现有 NIC：

- **CLI：**[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell：**[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a>查看已附加到虚拟机的 NIC

1. 使用分配有订阅“所有者”、“参与者”或“网络参与者”角色的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟机”。 当“虚拟机”出现在搜索结果中时，请单击它。
3. 在出现的“虚拟机”边栏选项卡中，单击要查看其附加网络接口的 VM 的名称。
4. 在针对所选 VM 显示的“虚拟机”边栏选项卡中，单击“网络接口”。

|**工具**|**命令**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>将 NIC 附加到现有的虚拟机

要将 NIC 附加到的 VM 必须支持多个 NIC 并处于停止（解除分配）状态。 无法使用 Azure 门户将 NIC 附加到现有的 VM。 可使用以下 Azure CLI 或 PowerShell 命令将 NIC 附加到 VM：

- **CLI：**[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell：**[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>从现有的虚拟机中分离 NIC

要从中分离 NIC 的 VM 必须处于停止（解除分配）状态，并且当前必须至少有两个附加的 NIC。 可以分离任何 NIC，但 VM 必须始终至少有一个附加的 NIC。 如果分离主 NIC，Azure 会将主属性分配给附加到 VM 时间最长的某个剩余 NIC。 你也可以自行将任何 NIC 指定为主 NIC。 无示使用 Azure 门户从 VM 中分离 NIC 或者设置 NIC 的主属性，但可以使用 CLI 或 PowerShell 完成这两项操作。 可使用以下 Azure CLI 或 PowerShell 命令从 VM 中分离 NIC：

- **CLI：**[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell：**[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>后续步骤
若要使用脚本创建具有多个 NIC 或 IP 配置的 VM，请参阅以下文章：

|**任务**|**工具**|
|---|---|
|**创建具有多个 NIC 的 VM**|[CLI](virtual-network-deploy-multinic-arm-cli.md) 和 [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**创建分配有多个 IP 地址的单 NIC VM**|[CLI](virtual-network-multiple-ip-addresses-cli.md) 和 [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

