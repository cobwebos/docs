---
title: "为 Azure 网络接口配置 IP 地址 | Microsoft Docs"
description: "了解如何为网络接口添加、更改和删除专用和公共 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: f6722365e5a5e4c58d91dd178de264a403d53c02
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---

# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>为 Azure 网络接口添加、更改或删除 IP 地址

了解如何为网络接口添加、更改和删除网公共和专用 IP 地址。 通过分配给网络接口的专用 IP 地址，虚拟机能够与 Azure 虚拟网络和所连接的网络中的其他资源进行通信。 通过专用 IP 地址，还能够使用不可预测的 IP 地址实现到 Internet 的出站通信。 通过分配给网络接口的[公共 IP 地址](virtual-network-public-ip-address.md)，可以实现从 Internet 到虚拟机的入站通信。 通过此地址，还能够使用不可预测的 IP 地址实现从虚拟机到 Internet 的出站通信。 有关详细信息，请参阅[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

如果需要创建、更改或删除网络接口，请阅读[管理网络接口](virtual-network-network-interface.md)一文。 如果需要向虚拟机添加网络接口或从中删除网络接口，请阅读[添加或删除网络接口](virtual-network-network-interface-vm.md)一文。 


## <a name="before-you-begin"></a>开始之前

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解对公共和专用 IP 地址的限制。
- 使用 Azure 帐户登录到 Azure [门户](https://portal.azure.com)、Azure 命令行接口 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令来完成本文中的任务，请[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请[安装并配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。 不用安装 CLI 及其必备组件，可以使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置与你的帐户一起使用。 若要使用 Cloud Shell，请单击[门户](https://portal.azure.com)顶部的 Cloud Shell“>_”按钮。

## <a name="add-ip-addresses"></a>添加 IP 地址

可将所需的任意数量的[专用](#private)和[公共](#public) [IPv4](#ipv4) 地址添加到网络接口，只要不超过 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文中列出的限制即可。 无法使用门户向现有网络接口添加 IPv6 地址（但是可以在创建网络接口时使用门户向网络接口添加专用 IPv6 地址）。 可以使用 PowerShell 或 CLI 向未附加到虚拟机的现有网络接口的一个[辅助 IP 配置](#secondary)添加专用 IPv6 地址（只要不存在现有的辅助 IP 配置即可）。 无法使用任何工具向网络接口添加公共 IPv6 地址。 有关使用 IPv6 地址的详细信息，请参阅 [IPv6](#ipv6)。 

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击要为其添加 IPv4 地址的网络接口。
4. 在所选网络接口的边栏选项卡的“设置”部分中，单击“IP 配置”。
5. 在打开的 IP 配置边栏选项卡中单击“+ 添加”。
6. 指定以下设置，并单击“确定”关闭“添加 IP 配置”边栏选项卡：

    |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|对于网络接口必须是唯一的|
    |类型|是|由于要将 IP 配置添加到现有网络接口，并且每个网络接口都必须有一个[主要](#primary) IP 配置，因此，你的唯一选项是“辅助”。|
    |专用 IP 地址分配方法|是|如果在虚拟机处于停止（解除分配）的状态下将它重新启动，[**动态**](#dynamic)地址可能会更改。 Azure 从网络接口连接到的子网的地址空间分配可用地址。 只有在删除网络接口之后，[**静态**](#static)地址才会释放。 从当前未由其他 IP 配置使用的子网地址空间范围中指定一个 IP 地址。|
    |公共 IP 地址|否|**已禁用：**该 IP 配置当前没有关联的公共 IP 地址资源。 **已启用：**选择现有的 IPv4 公共 IP 地址，或新建一个。 若要了解如何创建公共 IP 地址，请参阅[公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)一文。|
7. 可以遵循[将多个 IP 地址分配到虚拟机操作系统](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的说明，手动将辅助专用 IP 地址添加到虚拟机操作系统。 在手动向虚拟机操作系统添加 IP 地址之前，请参阅[专用](#private) IP 地址以了解特殊注意事项。 请不要向虚拟机操作系统添加任何公共 IP 地址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>更改 IP 地址设置

你可能需要更改 IPv4 地址的分配方法、更改静态 IPv4 地址，或者更改分配给网络接口的公共 IP 地址。 如果要更改与虚拟机中的辅助网络接口关联的辅助 IP 配置的专用 IPv4 地址（有关详细信息，请参阅[主要网络接口和辅助网络接口](virtual-network-network-interface-vm.md#about)），请将该虚拟机置于“已停止”（“已解除分配”）状态，然后完成以下步骤： 

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击想要查看或更改其 IP 地址设置的网络接口。
4. 在所选网络接口的边栏选项卡的“设置”部分中，单击“IP 配置”。
5. 在打开的 IP 配置边栏选项卡上显示的列表中，单击要修改的 IP 配置。
6. 参考本文[添加 IP 配置](#create-ip-config)部分步骤 6 中有关设置的信息，根据需要更改设置。 单击“保存”关闭所更改 IP 配置的边栏选项卡。

>[!NOTE]
>在 Windows 中，如果主要网络接口有多个 IP 配置，并且你更改了主要 IP 配置的专用 IP 地址，则必须手动将主要 IP 地址和辅助 IP 地址重新分配给网络接口（在 Linux 中不需要执行此操作）。 若要手动向操作系统中的网络接口分配 IP 地址，请参阅[将多个 IP 地址分配到虚拟机](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 在手动向虚拟机操作系统添加 IP 地址之前，请参阅[专用](#private) IP 地址以了解特殊注意事项。 请不要向虚拟机操作系统添加任何公共 IP 地址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>删除 IP 地址

可以从网络接口中删除[专用](#private)和[公共](#public) IP 地址，但网络接口必须始终至少有一个分配给它的专用 IPv4 地址。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击要从其中删除 IP 地址的网络接口。
4. 在所选网络接口的边栏选项卡的“设置”部分中，单击“IP 配置”。
5. 右键单击要删除的[辅助](#secondary) IP 配置（无法删除[主要](#primary)配置），单击“删除”，然后单击“是”确认删除。 如果该配置具有关联的公共 IP 地址资源，将从 IP 配置中取消关联该资源，但不会删除该资源。
6. 关闭“IP 配置”边栏选项卡。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP 配置

[专用](#private)和（可选）[公共](#public) IP 地址分配给一个或多个分配给网络接口的 IP 配置。 有两种 IP 配置：

### <a name="primary"></a>主要

每个网络接口都分配有一个主要 IP 配置。 主要 IP 配置：

- 具有分配给它的[专用](#private) [IPv4](#ipv4) 地址。 不能向主要 IP 配置分配专用 [IPv6](#ipv6) 地址。
- 还可能具有分配给它的[公共](#public) IPv4 地址。 不能向主要或辅助 IP 配置分配公共 IPv6 地址。 但是，可以向 Azure 负载均衡器分配公共 IPv6 地址，负载均衡器对发往虚拟机专用 IPv6 地址的流量进行负载均衡。 有关详细信息，请参阅 [IPv6 的详细信息和限制](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)。

### <a name="secondary"></a>辅助

除了主要 IP 配置之外，网络接口还可能具有零个或多个分配给它的辅助 IP 配置。 辅助 IP 配置：

- 必须具有分配给它的 IPv4 或 IPv6 地址。 如果地址是 IPv6，则网络接口只能具有一个辅助 IP 配置。 如果地址是 IPv4，则网络接口可以具有多个分配给它的辅助 IP 配置。 若要详细了解可以向网络接口分配多少专用和公共 IPv4 地址，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。  
- 如果专用 IP 地址是 IPv4，则还可以具有分配给它的多个公共 IPv4 地址。 如果专用 IP 地址是 IPv6，则无法向 IP 配置分配公共 IPv4 或 IPv6 地址。 在下列方案中，向网络接口分配多个 IP 地址很有帮助：
    - 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
    - 虚拟机充当网络虚拟设备，例如防火墙或负载均衡器。
    - 可将任何网络接口的任何专用 IPv4 地址添加到 Azure 负载均衡器后端池。 过去，只能将主要网络接口的主要 IPv4 地址添加到后端池。 若要详细了解如何对多个 IPv4 配置进行负载均衡，请参阅[对多个 IP 配置进行负载均衡](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 
    - 可以对分配给网络接口的一个 IPv6 地址进行负载均衡。 若要详细了解如何对 IPv6 地址进行负载均衡，请参阅[对 IPv6 地址进行负载均衡](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。


## <a name="address-types"></a>地址类型

可以向 [IP 配置](#ip-configurations)分配以下类型的 IP 地址：

### <a name="private"></a>专用

通过专用 [IPv4](#ipv4) 地址，虚拟机能够与虚拟网络或所连接的网络中的其他资源进行通信。 使用专用 [IPv6](#ipv6) 地址时，虚拟机无法用作入站通信的目标，也无法进行出站通信，但有一个例外。 虚拟机可以使用 IPv6 地址与 Azure 负载均衡器进行通信。 有关详细信息，请参阅 [IPv6 的详细信息和限制](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)。 

默认情况下，Azure DHCP 服务器将网络接口的[主要 IP 配置](#primary)的专用 IPv4 地址分配给虚拟机操作系统内的网络接口。 除非必要，永远不应当在虚拟机操作系统中手动设置网络接口的 IP 地址。 

> [!WARNING]
> 如果在虚拟机操作系统中设置为网络接口的主要 IP 地址的 IPv4 地址曾经不同于为附加到 Azure 中的虚拟机的主要网络接口的主要 IP 配置分配的专用 IPv4 地址，则会失去到虚拟机的连接。

在许多方案中，需要手动设置虚拟机操作系统内的网络接口的 IP 地址。 例如，在向 Azure 虚拟机添加多个 IP 地址时，必须手动设置 Windows 操作系统的主要和辅助 IP 地址。 对于 Linux 虚拟机，可能只需要手动设置辅助 IP 地址。 有关详细信息，请参阅[向 VM 操作系统添加 IP 地址](virtual-network-multiple-ip-addresses-portal.md#os-config)。 手动在操作系统中设置 IP 地址时，建议你始终使用静态（而非动态）分配方法为网络接口的 IP 配置分配地址。 使用静态分配方法可以确保地址在 Azure 内不会更改。 如果你需要更改分配给 IP 配置的地址，建议你：

1. 在操作系统中将 IP 地址的分配方法更改回 DHCP 并重新启动虚拟机，以确保虚拟机从 Azure DHCP 服务器收到地址。
2. 关闭（解除分配）虚拟机。
3. 在 Azure 中更改 IP 配置的 IP 地址。
4. 启动虚拟机。
5. 在操作系统中[手动配置](virtual-network-multiple-ip-addresses-portal.md#os-config)辅助 IP 地址（在 Windows 内还需要配置主要 IP 地址）以配置在 Azure.
 
通过遵循上述步骤，在 Azure 中分配给网络接口的专用 IP 地址与在虚拟机操作系统中分配的地址将不会不同。 若要记录你在操作系统中为订阅中的哪些虚拟机手动设置了 IP 地址，请考虑向虚拟机添加一个 Azure [标记](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)。 例如，你可以使用“IP 地址分配：静态”。 这样，可以轻松找到你的订阅中你在操作系统中手动为其设置了 IP 地址的虚拟机。

通过专用 IP 地址，虚拟机除了能够与同一网络中的或所连接的虚拟网络中的其他资源进行通信外，还能够进行到 Internet 的出站通信。 出站连接是由 Azure 转换为不可预测的公共 IP 地址的源网络地址。 若要了解 Azure 出站 Internet 连接的详细信息，请阅读 [Azure 出站 Internet 连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 不能从 Internet 进行到虚拟机的专用 IP 地址的入站通信。

### <a name="public"></a>公共

通过公共 IP 地址，可以建立从 Internet 到虚拟机的入站连接。 到 Internet 的出站连接使用不可预测的 IP 地址。 有关详细信息，请参阅[了解 Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 可以将公共 IP 地址分配给 IP 配置，但这不是必需的。 如果没有向虚拟机分配公共 IP 地址，则它仍然可以使用其专用 IP 地址进行到 Internet 的出站通信。 若要详细了解公共 IP 地址，请阅读[公共 IP 地址](virtual-network-public-ip-address.md)一文。

可分配给网络接口的公共和专用 IP 地址数有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

> [!NOTE]
> Azure 会将虚拟机的专用 IP 地址转换为公共 IP 地址。 因此，操作系统不会意识到分配给它的任何公共 IP 地址，因此不需要在操作系统中手动分配公共 IP 地址。

## <a name="assignment-methods"></a>分配方法

公共和专用 IP 地址是使用以下分配方法分配的：

### <a name="dynamic"></a>动态

默认情况下会分配动态专用 IPv4 和 IPv6（可选）地址。 如果将虚拟机置于“已停止”（“已解除分配”）状态，然后将其启动，则动态地址可能会更改。 如果在虚拟机生存期内不希望 IPv4 地址更改，请使用静态方法分配地址。 使用动态分配方法只能分配专用 IPv6 地址。 无法使用任一方法为 IP 配置分配公共 IPv6 地址。

### <a name="static"></a>静态

使用静态方法分配的地址不会更改，直到虚拟机被删除。 可以从网络接口所在的子网的地址空间手动为 IP 配置分配静态专用 IPv4 地址。 （可选）可以为 IP 配置分配公共或专用静态 IPv4 地址。 无法为 IP 配置分配静态公共或专用 IPv6 地址。 若要详细了解 Azure 如何分配静态公共 IPv4 地址，请参阅[公共 IP 地址](virtual-network-public-ip-address.md)一文。

## <a name="ip-address-versions"></a>IP 地址版本

在分配地址时可以指定以下版本：

### <a name="ipv4"></a>IPv4

每个网络接口必须有一个具有分配的[专用](#private) [IPv4](#ipv4) 地址的[主要](#primary) IP 配置。 可以添加一个或多个[辅助](#secondary) IP 配置，每个配置都具有一个 IPv4 专用地址（可选）和一个 IPv4 [公共](#public) IP 地址。

### <a name="ipv6"></a>IPv6

可以为网络接口的辅助 IP 配置分配零个或一个专用 [IPv6](#ipv6) 地址。 网络接口不能具有任何现有的辅助 IP 配置。 无法使用门户添加具有 IPv6 地址的 IP 配置。 若要向现有网络接口添加具有专用 IPv6 地址的 IP 配置，必须使用 PowerShell 或 CLI。 该网络接口无法附加到现有 VM。

> [!NOTE]
> 虽然可以使用门户创建具有 IPv6 地址的网络接口，但是，在使用门户创建虚拟机时，无法创建具有专用 IPv6 地址的虚拟机，也无法附加网络接口。 必须使用 PowerShell 或 Azure CLI 2.0 创建具有专用 IPv6 地址的网络接口，然后在创建虚拟机时附加该网络接口。 无法将分配有专用 IPv6 地址的网络接口附加到现有虚拟机。 对于附加到虚拟机的任何网络接口，无法使用任何工具（门户、CLI 或 PowerShell）为 IP 配置添加专用 IPv6 地址。

无法为主要或辅助 IP 配置分配公共 IPv6 地址。

## <a name="next-steps"></a>后续步骤
若要创建具有不同 IP 配置的虚拟机，请阅读以下文章：

**命令**

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IPv4 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|创建具有专用 IPv6 地址的单 NIC VM（在 Azure 负载均衡器后面）|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure 资源管理器模板](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

