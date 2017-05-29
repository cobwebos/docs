---
title: "为 Azure 网络接口配置 IP 地址 | Microsoft Docs"
description: "了解如何添加、更改和删除 NIC 的公共和专用 IP 地址。"
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: zh-cn
ms.lasthandoff: 05/13/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>为 Azure 网络接口添加、更改或删除 IP 地址

了解如何添加、更改和删除网络接口 (NIC) 的公共和专用 IP 地址。 通过分配给 NIC 的专用 IP 地址，VM 可以与 Internet 以及连接到 Azure 虚拟网络 (VNet) 的其他资源通信。 使用分配给 NIC 的公共 IP 地址，可以从 Internet 进行 VM 的入站通信。 

若需创建、更改或删除 NIC，请阅读 [NIC 设置和任务](virtual-network-network-interface.md)一文。 如果需要在虚拟机中添加或删除 NIC，请阅读[添加或删除 NIC](virtual-network-network-interface-vm.md) 一文。 


## <a name="before"></a>准备工作

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解对公共和专用 IP 地址的限制。
- 使用 Azure 帐户登录到 Azure 门户、Azure 命令行界面 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令完成本文中的任务，请按[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure PowerShell。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行界面 (CLI) 命令完成本文中的任务，请按[如何安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure CLI。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。

## <a name="about"></a>关于 NIC 和 IP 地址

可以为每个 NIC 分配多个专用和公共 IP 地址，只要符合 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)即可。 在如下所述的场景中，向 NIC 分配多个 IP 地址会很有帮助：
- 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
- VM 充当网络虚拟设备，例如防火墙或负载均衡器。
- 可将任何 NIC 的任何专用 IP 地址添加到 Azure 负载均衡器后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要详细了解如何对多个 IP 配置进行负载均衡，请阅读[对多个 IP 配置进行负载均衡](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

IP 地址分配给 IP 配置。 始终为 NIC 分配一个**主** IP 配置，但也可为其分配多个**辅助**配置。 将为每个 IP 配置分配以下地址类型中的一种或两种：
- **专用：**VM 可以通过专用 IP 地址与连接到 NIC 所在的 VNet 的其他资源通信。 一个 IP 配置必须分配一个专用 IP 地址。 Azure DHCP 服务器将 NIC 主 IP 配置的专用 IP 地址分配到 VM 操作系统中的 NIC。 VM 也可通过专用 IP 地址与 Internet 进行出站通信。 出站连接是转换的源网络地址 (SNAT)。 若要了解 Azure 出站 Internet 连接的详细信息，请阅读 [Azure 出站 Internet 连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 不能从 Internet 与 VM 的专用 IP 地址进行入站通信。
- **公共：**可以从 Internet 通过公共 IP 地址与 VM 进行入站连接。 到 Internet 的出站连接未进行 SNAT 处理。 可以将公共 IP 地址分配给 IP 配置，但这不是必需的。 若要详细了解公共 IP 地址，请阅读[公共 IP 地址](virtual-network-public-ip-address.md)一文。

可分配到 NIC 的公共和专用 IP 地址数有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

可以使用以下分配方法来分配公共和专用 IP 地址：
- **动态：**默认情况下会分配动态专用和公共 IP 地址。 如果将 VM 置于“已停止”（“已解除分配”）状态，然后将其重新启动，动态地址可能会更改。 如果不希望 IP 地址在 VM 的生命周期内更改，请使用静态地址。
- **静态：**静态地址不会更改，除非将 VM 删除。 可以从 NIC 所附加到的地址空间为子网分配静态专用 IP 地址。 若要详细了解 Azure 如何分配静态公共 IP 地址，请阅读[公共 IP 地址](virtual-network-public-ip-address.md)一文。

## <a name="create-ip-config"></a>添加 IP 地址

可将任意数量的 IP 地址添加到 NIC，只要不超过 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文中所列的限制即可。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击要为其添加 IP 地址的 NIC。
4. 在所选 NIC 的边栏选项卡的“设置”部分，单击“IP 配置”。
5. 在打开的 IP 配置边栏选项卡中单击“+ 添加”。
6. 指定以下设置，然后单击“确定”关闭“添加 IP 配置”边栏选项卡：

    |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|必须是唯一的 NIC 名称|
    |类型|是|由于你要将 IP 配置添加到现有 NIC，而每个 NIC 都必须有一个主 IP 配置，因此，唯一的选项是“辅助”。|
    |专用 IP 地址分配方法|是|如果在 VM 处于停止（解除分配）的状态下将它重新启动，**动态**地址可能会更改。 Azure 从 NIC 连接到的子网的地址空间分配可用地址。 只有在删除 NIC 之后，**静态**地址才会释放。 从当前未由其他 IP 配置使用的子网地址空间范围中指定一个 IP 地址。|
    |公共 IP 地址|否|**已禁用：**该 IP 配置当前没有关联的公共 IP 地址资源。 **已启用：**选择现有的公共 IP 地址，或新建一个。 若要了解如何创建公共 IP 地址，请参阅[公共 IP 地址](virtual-network-public-ip-address.md#create)一文。|
7. 可以遵循[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的说明，手动将辅助专用 IP 地址添加到 VM 操作系统。 请不要将任何公共 IP 地址添加到 VM 操作系统。

命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>更改 IP 地址设置

可能需要更改 IP 地址的分配方法、更改静态 IP 地址，或者更改分配到 NIC 的公共 IP 地址。 如果要更改与 VM 中的辅助 NIC（有关详细信息，请参阅[主 NIC 和辅助 NIC](virtual-network-network-interface-vm.md#about)）关联的辅助 IP 配置的专用 IP 地址，请将该 VM 置于“已停止”（“已解除分配”）状态，然后再完成以下步骤： 

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击想要查看或更改其 IP 地址设置的 NIC。
4. 在所选 NIC 的边栏选项卡的“设置”部分，单击“IP 配置”。
5. 在打开的 IP 配置边栏选项卡上显示的列表中，单击要修改的 IP 配置。
6. 参考本文[添加 IP 配置](#create-ip-config)部分步骤 6 中有关设置的信息，根据需要更改设置。 单击“保存”关闭所更改 IP 配置的边栏选项卡。

>[!NOTE]
>如果主 NIC 有多个 IP 配置，而你更改了主 IP 配置的专用 IP 地址，则必须手动将所有辅助 IP 地址重新分配到 Windows 中的 NIC（在 Linux 中不需要执行此操作）。 若要手动将 IP 地址分配到操作系统中的 NIC，请参阅[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 请不要将任何公共 IP 地址添加到 VM 操作系统。

命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>删除 IP 地址

可以从 NIC 中删除专用和公共 IP 地址，但 NIC 必须始终有一个分配的专用 IP 地址。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击要从其删除 IP 地址的 NIC。
4. 在所选 NIC 的边栏选项卡的“设置”部分，单击“IP 配置”。
5. 右键单击要删除的辅助 IP 配置（不能删除主配置），单击“删除”，然后单击“是”确认删除。 如果该配置具有关联的公共 IP 地址资源，将从 IP 配置中取消关联该资源，但不会删除该资源。
6. 关闭“IP 配置”边栏选项卡。

命令

|工具|命令|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>后续步骤
若要创建具有多个 NIC 或 IP 地址的 VM，请阅读以下文章：

命令

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IP 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

