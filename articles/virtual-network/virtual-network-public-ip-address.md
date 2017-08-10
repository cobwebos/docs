---
title: "创建、更改或删除 Azure 公共 IP 地址 | Microsoft Docs"
description: "了解如何创建、更改或删除公共 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 41e4fe685f38a6a19a2024a12198cb8a8566182e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---

# <a name="create-change-or-delete-a-public-ip-address"></a>创建、更改或删除公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除此类地址。 公共 IP 地址是具有自身可配置设置的资源。 将公共 IP 地址分配到其他 Azure 资源可实现以下目的：
- 建立与 Azure 虚拟机 (VM)、Azure 虚拟机规模集、Azure VPN 网关、应用程序网关和面向 Internet 的 Azure 负载均衡器等资源的入站 Internet 连接。 若未分配公共 IP 地址，Azure 资源无法接收来自 Internet 的入站通信。 虽然本质上可通过公共 IP 地址访问某些 Azure 资源，但其他资源必须分配有公共 IP 地址才可从 Internet 访问它们。
- 使用可预测的 IP 地址与 Internet 建立出站连接。 例如，如果某虚拟机未分配有公共 IP 地址，但其地址由 Azure 网络地址转换为可预测的公共地址，则该虚拟机可与 Internet 建立出站通信。 通过将公共 IP 地址分配给资源，可了解哪个 IP 地址用于出站连接。 尽管可预测，但地址可根据所选分配方法进行更改。 有关详细信息，请参阅[创建公共 IP 地址](#create-a-public-ip-address)。 有关从 Azure 资源建立出站连接的详细信息，请阅读 [Understand outbound connections](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（了解出站连接）一文。

## <a name="before-you-begin"></a>开始之前

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解对公共 IP 地址的限制。
- 使用 Azure 帐户登录到 Azure [门户](https://portal.azure.com)、Azure 命令行接口 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令来完成本文中的任务，请[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请[安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。 请勿安装 CLI 及其必备组件，可使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置与你的帐户一起使用。 若要使用 Cloud Shell，请单击[门户](https://portal.azure.com)顶部的 Cloud Shell >_ 按钮。

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中单击“+ 添加”。
4. 在出现的“创建公共 IP 地址”边栏选项卡中，输入或选择以下设置的值，并单击“创建”：

    |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|该名称在所选的资源组中必须唯一。|
    |IP 版本|是| 选择 IPv4 或 IPv6。 虽然可将公共 IPv4 地址分配给多个 Azure 资源，但只可将 IPv6 公共 IP 地址分配给面向 Internet 的负载均衡器。 负载均衡器可将 IPv6 流量负载均衡到 Azure 虚拟机。 详细了解如何[将 IPv6 流量负载均衡到虚拟机](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    |IP 地址分配|是|**动态：**只有在将公共 IP 地址与附加到 VM 的 NIC 相关联并首次启动该 VM 时，才分配动态地址。 如果附加到 VM 的 NIC 已停止（解除分配），动态地址可能会更改。 如果 VM 重新启动或停止（但未解除分配），该地址将保持不变。 **静态：**静态地址是在创建公共 IP 地址时分配的。 即使 VM 处于停止（解除分配）状态，静态地址也不会发生更改。 仅当删除 NIC 时，才释放该地址。 创建 NIC 后，可以更改分配方法。 如果选择 IPv6 作为“IP 版本”，则仅可使用“动态”分配方法。|
    |空闲超时（分钟）|否|不依赖于客户端发送 keep-alive 消息，将 TCP 或 HTTP 连接保持打开的分钟数。 如果选择 IPv6 作为“IP 版本”，则不能更改此值。 |
    |DNS 名称标签|否|必须在创建该名称的 Azure 位置（所有订阅和所有客户位置）中保持唯一。 Azure 公共 DNS 服务会自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 将 *location.cloudapp.azure.com*（其中的 location 是所选的位置）追加到提供的名称后面，创建完全限定的 DNS 名称。 如果选择同时创建这两个地址版本，则会将相同的 DNS 名称分配给 IPv4 和 IPv6 地址。 Azure DNS 服务包含 IPv4 A 和 IPv6 AAAA 名称记录，并在查找 DNS 名称时对这两种记录做出响应。 客户端选择要与哪个地址（IPv4 或 IPv6）通信。|
    |创建 IPv6（或 IPv4）地址|否| 为“IP 版本”选择的选项决定了是显示 IPv6 还是 IPv4。 例如，如果选择 IPv4 作为“IP 版本”，则此处将显示 IPv6。
    |名称（仅在勾选“创建 IPv6 (或 IPv4)地址”复选框时可见）|是，如果选择“创建 IPv6”（或 IPv4）复选框。|该名称必须不同于在此列表中的第一个“名称”中输入的名称。 如果选择同时创建 IPv4 和 IPv6 地址，门户将创建两个单独的公共 IP 地址资源，每个资源中分配有一个 IP 地址版本。|
    |IP 地址分配（仅在“创建 IPv6 (或 IPv4)地址”复选框时可见）|是，如果选择“创建 IPv6”（或 IPv4）复选框。|如果复选框显示“创建 IPv4 地址”，可选择分配方法。 如果复选框显示“创建 IPv6 地址”，则不能选择分配方法，因为必须采用“动态”方法。|
    |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
    |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
    |位置|是|必须与要将公共 IP 地址关联到的资源位于同一[位置](https://azure.microsoft.com/regions)（也称为“区域”）。|

**命令**

虽然门户提供了用于创建两个公共 IP 地址资源（一个 IPv4 和一个 IPv6）的选项，但以下 CLI 和 PowerShell 命令可使用任一 IP 版本的地址创建一个资源。 如果需要两个公共 IP 地址资源（每个 IP 版本一个），必须运行此命令两次，为公共 IP 地址资源指定不同的名称和版本。 

|工具|命令|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>查看、删除公共 IP 地址或更改其设置

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中，单击要查看、更改其设置或要删除的公共 IP 地址的名称。
4. 在出现的公共 IP 地址边栏选项卡中，根据是要查看、删除还是更改公共 IP 地址，完成以下选项之一。
    - **查看**：边栏选项卡的“概述”部分显示公共 IP 地址的主要设置，例如与之关联的网络接口（如果地址与某个网络接口关联）。 门户不显示地址的版本（IPv4 或 IPv6）。 若要查看版本信息，请使用 PowerShell 或 CLI 命令查看公共 IP 地址。 如果 IP 地址版本为 IPv6，则门户、PowerShell 或 CLI 不显示分配的地址。 
    - **删除**：若要删除公共 IP 地址，请在边栏选项卡的“概述”部分中单击“删除”。 如果该地址当前已关联到某个 IP 配置，则无法将其删除。 如果该地址当前已关联到某个配置，请单击“取消关联”，从该 IP 配置中取消关联该地址。
    - **更改**：单击“配置”。 使用本文[创建公共 IP 地址](#create-a-public-ip-address)部分所述的步骤 4 中的信息更改设置。 要将 IPv4 地址的分配方法从静态更改为动态，必须先从该公共 IPv4 地址关联的 IP 配置中取消关联该地址。 然后，可将分配方法更改为动态，并单击“关联”将该 IP 地址关联到相同或不同的 IP 配置，或者让它保持取消关联状态。 若要取消关联公共 IP 地址，请在“概述”部分中单击“取消关联”。

>[!WARNING]
>将分配方法从静态更改为动态时，将丢失已分配到公共 IP 地址的 IP 地址。 尽管 Azure 公共 DNS 服务器会保留静态或动态地址与任何 DNS 名称标签（如果已定义）之间的映射，但如果 VM 在处于停止（解除分配）状态之后启动，动态 IP 地址可能会更改。 若要防止地址更改，请分配静态 IP 地址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 用于列出公共 IP 地址；[az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 用于显示设置；[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) 用于更新；[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) 用于删除|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 用于检索公共 IP 地址对象并查看其设置；[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 用于更新设置；[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) 用于删除|

## <a name="next-steps"></a>后续步骤
创建以下 Azure 资源时分配公共 IP 地址：

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机
- [面向 Internet 的 Azure 负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 应用程序网关](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Azure VPN 网关建立站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

