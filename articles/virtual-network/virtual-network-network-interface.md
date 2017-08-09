---
title: "创建、更改或删除 Azure 网络接口 | Microsoft Docs"
description: "了解什么是网络接口，以及如何创建、删除网络接口及更改其设置。"
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
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 37405fb9e8b85040fbbd1e91efd44a80c4f87ff0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---

# <a name="create-change-or-delete-a-network-interface"></a>创建、更改或删除网络接口

了解如何创建、删除网络接口及更改其设置。 Azure 虚拟机可通过网络接口与 Internet、Azure 及本地资源进行通信。 使用 Azure 门户创建虚拟机时，门户将使用默认设置创建一个网络接口。 可改为选择使用自定义设置创建网络接口，并在创建虚拟机时向其添加一个或多个网络接口。 还可更改现有网络接口的默认网络接口设置。 本文介绍如何使用自定义设置创建网络接口、更改现有设置（例如网络筛选器（网络安全组）分配、子网分配、DNS 服务器设置和 IP 转发）以及删除网络接口。

如果需要为网络接口添加、更改或删除 IP 地址，请阅读[管理 IP 地址](virtual-network-network-interface-addresses.md)一文。 如果需要向虚拟机添加网络接口或从中删除网络接口，请阅读[添加或删除网络接口](virtual-network-network-interface-vm.md)一文。


## <a name="before-you-begin"></a>开始之前

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解网络接口的限制。
- 使用 Azure 帐户登录到 Azure [门户](https://portal.azure.com)、Azure 命令行接口 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令来完成本文中的任务，请[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请[安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。 请勿安装 CLI 及其必备组件，可使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置与你的帐户一起使用。 若要使用 Cloud Shell，请单击[门户](https://portal.azure.com)顶部的 Cloud Shell >_ 按钮。

## <a name="create-a-network-interface"></a>创建网络接口

使用 Azure 门户创建虚拟机时，门户将使用默认设置创建一个网络接口。 如果想要指定所有网络接口设置，可使用自定义设置创建网络接口并在创建虚拟机（使用 PowerShell 或 Azure CLI）时向其附加此网络接口。 还可创建网络接口并将其添加到现有的虚拟机（使用 PowerShell 或 Azure CLI）。 若要了解如何创建包含现有网络接口的虚拟机，或者要在现有虚拟机中添加或删除网络接口，请阅读[添加或删除网络接口](virtual-network-network-interface-vm.md)一文。 创建网络接口之前，创建该接口时所在的同一位置和订阅中必须存在现有的[虚拟网络](virtual-networks-create-vnet-arm-pportal.md)。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在出现的“网络接口”边栏选项卡中，单击“+ 添加”。
4. 在出现的“创建网络接口”边栏选项卡中，输入或选择以下设置的值，然后单击“创建”：

    |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|该名称在所选的资源组中必须唯一。 随着时间推移，Azure 订阅中可能会有多个网络接口。 请阅读[命名约定](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文，获取有关创建命名约定来简化多个网络接口的管理的建议。 创建网络接口后，无法更改其名称。|
    |虚拟网络|是|为网络接口选择虚拟网络。 只能将网络接口分配到与该接口位于相同订阅和位置的虚拟网络。 创建网络接口后，无法更改其分配到的虚拟网络。 将网络接口添加到的虚拟机也必须位于该接口所在的同一位置和订阅中。|
    |子网|是|在所选的虚拟网络中选择一个子网。 创建网络接口后，可更改它分配到的子网。|
    |专用 IP 地址分配|是| 在此设置中，将为 IPv4 地址选择分配方法。 从以下分配方法中选择：**动态：**选择此选项时，Azure 将从所选子网的地址空间中自动分配可用地址。 当网络接口所在的虚拟机处于停止（解除分配）状态后启动时，Azure 会向网络接口分配一个不同的地址。 如果在虚拟机未处于停止（解除分配）状态时将其重启，地址将保持不变。 **静态：**选择此选项时，必须手动从所选子网的地址空间中手动分配一个可用的 IP 地址。 静态地址保持不变，除非手动更改或删除网络接口。 创建网络接口后，可更改分配方法。 Azure DHCP 服务器将此地址分配到虚拟机操作系统中的网络接口。|
    |网络安全组|否| 保留设置为“无”，选择现有的[网络安全组](virtual-networks-nsg.md)，或[创建网络安全组](virtual-networks-create-nsg-arm-pportal.md)。 网络安全组可用于筛选进出网络接口的网络流量。 可向网络接口应用零个或一个网络安全组。 也可向网络接口分配到的子网应用零个或一个网络安全组。 将网络安全组应用到网络接口以及该接口分配到的子网时，有时会产生意外结果。 若要对应用到网络接口和子网的网络安全组进行故障排除，请阅读[网络安全组故障排除](virtual-network-nsg-troubleshoot-portal.md#nsg)一文。|
    |订阅|是|选择一个 Azure [订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 网络接口附加到的虚拟机及其连接到的虚拟网络必须位于同一订阅中。|
    |专用 IP 地址 (IPv6)|否| 如果选中此复选框，除了分配到网络接口的 IPv4 地址外，还会分配一个 IPv6 地址到网络接口。 请参阅本文的 [IPv6](#IPv6) 部分，了解有关 IPv6 和网络接口搭配使用的重要信息。 无法为 IPv6 地址选择分配方法。 如果选择分配 IPv6 地址，则会使用动态方法分配它。
    |IPv6 名称（仅在选中“专用 IP 地址 (IPv6)”复选框时出现） |是，如果选中“专用 IP 地址 (IPv6)”复选框。| 此名称将分配给网络接口的辅助 IP 配置。 在本文的 [查看网络接口设置](#view-network-interface-settings)部分详细了解相关 IP 配置。|
    |资源组|是|选择现有的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)或创建一个资源组。 网络接口可与它附加到的虚拟机或者连接到的虚拟网络位于相同或不同的资源组中。|
    |位置|是|网络接口附加到的虚拟机及其连接到的虚拟网络必须位于同一[位置](https://azure.microsoft.com/regions)（也称为区域）中。|

创建网络接口时，门户不会提供向接口分配公共 IP 地址的选项，但使用门户创建虚拟机时，门户会创建一个公共 IP 地址并将其分配到网络接口。 若要了解创建网络接口后如何向其添加公共 IP 地址，请阅读[管理 IP 地址](virtual-network-network-interface-addresses.md)一文。 若要使用公共 IP 地址创建网络接口，必须使用 CLI 或 PowerShell 创建网络接口。

>[!Note]
> 只有在网络接口附加到虚拟机后首次启动虚拟机时，Azure 才向网络接口分配 MAC 地址。 无法自行指定 Azure 要分配给网络接口的 MAC 地址。 除非网络接口被删除或者分配给主网络接口的主 IP 配置的专用 IP 地址发生更改，否则该 MAC 地址将始终分配给该网络接口。 若要详细了解 IP 地址和 IP 配置，请阅读[管理 IP 地址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>查看网络接口设置

创建网络接口后，可查看和更改其大多数设置。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击想要查看或更改其设置的网络接口。
4. 针对所选网络接口显示的边栏选项卡中将列出以下设置：
    - **概述：**提供网络接口的相关信息，例如分配给它的 IP 地址、网络接口分配到的虚拟网络/子网，以及网络接口附加到的虚拟机（若已附加到某虚拟机）。 下图显示名为 mywebserver256 的网络接口的概述设置：![网络接口概述](./media/virtual-network-network-interface/nic-overview.png)。可单击“资源组”或“订阅名称”旁边的“(更改)”，将网络接口移到其他资源组或订阅。 如果移动网络接口，必须同时移动与该网络接口相关的所有资源。 例如，如果网络接口已附加到虚拟机，则还必须移动该虚拟机及其相关的其他资源。 要移动网络接口，请阅读[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)一文。 该文章列出了先决条件，以及如何使用 Azure 门户、PowerShell 和 Azure CLI 移动资源。
    - **IP 配置：**此处列出分配到 IP 配置的公共和专用 IPv4 及 IPv6 地址。 如果向 IP 配置分配了 IPv6 地址，该地址不会显示。 在[为 Azure 网络接口配置 IP 地址](virtual-network-network-interface-addresses.md)一文中，详细了解 IP 配置以及如何添加和删除 IP 地址。 此部分还配置了 IP 转发和子网分配。 若要详细了解这些设置，请阅读本文的[启用/禁用 IP 转发](#enable-or-disable-ip-forwarding)和[更改子网分配](#change-subnet-assignment)部分。
    - **DNS 服务器：**可指定 Azure DHCP 服务器向网络接口分配哪个 DNS 服务器。 网络接口可从其连接到的虚拟网络继承设置，或使用自定义设置来替代其分配到的虚拟网络的设置。 若要修改显示的内容，请完成本文[更改 DNS 服务器](#change-dns-servers)部分中的步骤。
    - **网络安全组 (NSG)：**显示与网络接口关联的 NSG（若有）。 NSG 包含用于筛选网络接口网络流量的入站和出站规则。 如果网络接口关联有 NSG，会显示关联的 NSG 的名称。 若要修改显示的内容，请完成[管理网络安全组关联](virtual-network-manage-nsg-arm-portal.md#manage-associations)一文中的步骤。
    - **属性：**显示有关网络接口的关键设置，包括其 MAC 地址（若网络接口未附加到虚拟机，则为空）及其所在的订阅。
    - **有效的安全规则：**如果网络接口已附加到正在运行的虚拟机，且某 NSG 已关联到该接口和/或其分配到的子网，则会列出安全规则。 若要详细了解显示的内容，请参阅[网络安全组故障排除](virtual-network-nsg-troubleshoot-portal.md#nsg)一文。 有关 NSG 的详细信息，请参阅[网络安全组](virtual-networks-nsg.md)一文。
    - **有效的路由：**如果网络接口已附加到正在运行的虚拟机，则会列出路由。 路由是 Azure 默认路由、任何用户定义的路由 (UDR) 以及网络接口分配到的子网可能存在的任何 BGP 路由的组合。 若要详细了解显示的内容，请参阅[路由故障排除](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)一文。 若要详细了解 Azure 默认值和 UDR，请阅读[用户定义的路由](virtual-networks-udr-overview.md)一文。
    - **常见的 Azure 资源管理器设置：**若要详细了解常见的 Azure 资源管理器设置，请参阅文章[活动日志](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[访问控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[标记](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[锁定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自动化脚本](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)。

**命令**

如果将 IPv6 地址分配到网络接口，PowerShell 输出将返回信息表示地址已分配，但不返回分配的地址。 同样，CLI 将返回信息表示地址已分配，但在其地址的输出中返回 null。

|工具|命令|
|---|---|
|CLI|使用 [az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 查看订阅中的网络接口；使用 [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 查看网络接口的设置|
|PowerShell|使用 [Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) 查看订阅中的网络接口或查看网络接口的设置|

## <a name="change-dns-servers"></a>更改 DNS 服务器

DNS 服务器由 Azure DHCP 服务器分配到虚拟机操作系统中的网络接口。 分配的 DNS 服务器设置就是网络接口的 DNS 服务器设置。 若要详细了解网络接口的名称解析设置，请参阅[虚拟机的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。 网络接口可从虚拟网络继承设置，或使用自身唯一的设置替代虚拟网络的设置。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击想要查看或更改其设置的网络接口。
4. 在所选网络接口的边栏选项卡中，单击“设置”下面的“DNS 服务器”。
5. 单击以下选项之一：
    - **从虚拟网络继承(默认)**：选择此选项会继承针对网络接口分配到的虚拟网络定义的 DNS 服务器设置。 自定义 DNS 服务器或 Azure 提供的 DNS 服务器会在虚拟网络级别定义。 Azure 提供的 DNS 服务器可解析分配到同一虚拟网络的资源的主机名。 必须使用 FQDN 解析分配到不同虚拟网络的资源。
    - **自定义**：可自行配置 DNS 服务器来解析多个虚拟网络中的名称。 输入要用作 DNS 服务器的服务器的 IP 地址。 指定的 DNS 服务器地址仅分配到此网络接口，并将替代该网络接口分配到的虚拟网络的任何 DNS 设置。
6. 单击“保存” 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>启用/禁用 IP 转发

IP 转发使网络接口附加到的虚拟机能够：
- 接收未针对分配给任一网络接口 IP 配置的 IP 地址的网络流量。
- 使用与分配给某一网络接口 IP 配置的源 IP 地址不同的地址发送网络流量。

必须为附加到虚拟机并接收虚拟机需转发的流量的每个网络接口启用该设置。 无论虚拟机上附加了一个还是多个网络接口，该虚拟机都可转发流量。 尽管 IP 转发是一项 Azure 设置，但虚拟机也必须运行某个应用程序（例如防火墙、WAN 优化和负载均衡应用程序）才能转发流量。 运行网络应用程序的虚拟机通常称为网络虚拟设备。 可在 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中查看可直接部署的网络虚拟设备列表。 IP 转发通常与用户定义的路由配合使用。 若要详细了解用户定义的路由，请阅读[用户定义的路由](virtual-networks-udr-overview.md)一文。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在出现的“网络接口”边栏选项卡中，单击要为其启用或禁用 IP 转发的网络接口。
4. 在所选网络接口的边栏选项卡中，单击“设置”部分中的“IP 配置”。
5. 单击“已启用”或“禁用”（默认设置）来更改设置。
6. 单击“保存” 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>更改子网分配

可更改网络接口分配到的子网，但无法更改它分配到的虚拟网络。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 在显示的“网络接口”边栏选项卡中，单击想要查看或更改其设置的网络接口。
4. 在所选网络接口的边栏选项卡中的“设置”下面，单击“IP 配置”。 如果所列任何 IP 配置的任何专用 IP 地址旁边出现“(静态)”，则必须完成以下步骤，将 IP 地址分配方法更改为动态。 必须使用动态分配方法分配所有专用 IP 地址，以更改网络接口的子网分配。 如果使用动态方法分配地址，请转到步骤 5。 如果使用静态分配方法分配了任何 IPv4 地址，请完成以下步骤，将分配方法更改为动态：
    - 在 IP 配置列表中，单击想要更改其 IPv4 地址分配方法的 IP 配置。
    - 在显示的 IP 配置边栏选项卡中，选择“动态”作为“分配”方法。 无法使用静态分配方法分配 IPv6 地址。
    - 单击“保存” 。
5. 从“子网”下拉列表中选择要将网络接口连接到的子网。
6. 单击“保存” 。 新的动态地址是从新子网的子网地址范围中分配的。 将网络接口分配到新子网之后，可在需要时从新子网地址范围中分配静态 IPv4 地址。 若要详细了解如何添加、更改和删除网络接口的 IP 地址，请阅读[管理 IP 地址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>删除网络接口

只要网络接口未附加到虚拟机，即可删除此接口。 如果已将其附加到虚拟机，则必须先将虚拟机置于停止（解除分配）状态，再从虚拟机分离网络接口，然后才能删除网络接口。 若要从虚拟机中分离网络接口，请完成[添加或删除网络接口](virtual-network-network-interface-vm.md)一文的[从虚拟机中分离网络接口](virtual-network-network-interface-vm.md#vm-remove-nic)部分中的步骤。 删除虚拟机会分离其上附加的所有网络接口，但不会删除网络接口。

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“网络接口”。 当“网络接口”出现在搜索结果中时，请单击它。
3. 右键单击要删除的网络接口，并单击“删除”。
4. 单击“是”确认删除该网络接口。

删除网络接口时，会释放已分配给它的所有 MAC 或 IP 地址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>后续步骤
若要创建具有多个网络接口或 IP 地址的虚拟机，请阅读以下文章：

**命令**

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IPv4 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|创建具有专用 IPv6 地址的单 NIC VM（在 Azure 负载均衡器后）|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure 资源管理器模板](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

