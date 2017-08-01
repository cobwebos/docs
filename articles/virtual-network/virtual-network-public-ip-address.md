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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 47237fe499cd9244266487cd97f6be0d2cb2e977
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>创建、更改或删除公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除公共 IP 地址。 公共 IP 地址是具有自身可配置设置的资源。 将公共 IP 地址分配到其他 Azure 资源可实现以下目的：
- 与 Azure 虚拟机 (VM)、Azure 虚拟机规模集、Azure VPN 网关和面向 Internet 的 Azure 负载均衡器等资源建立入站 Internet 连接。
- 与未经过网络地址转换 (NAT) 的 Internet 建立出站连接。 例如，VM 可与未分配公共 IP 地址，但其地址已经过 Azure 网络地址转换的 Internet 进行出站通信。 有关从 Azure 资源建立出站连接的详细信息，请阅读 [Understand outbound connections](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（了解出站连接）一文。

本文说明如何使用通过 Azure Resource Manager 部署模型部署的公共 IP 地址。

## <a name="before"></a>准备工作

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解对公共 IP 地址的限制。
- 使用 Azure 帐户登录到 Azure 门户、Azure 命令行接口 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令完成本文中的任务，请按[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure PowerShell。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行接口 (CLI) 命令完成本文中的任务，请按[如何安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure CLI。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 

## <a name="create"></a>创建公共 IP 地址

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中单击“+ 添加”。
4. 在出现的“创建公共 IP 地址”边栏选项卡中，输入或选择以下设置的值，然后单击“创建”：

    |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|该名称在所选的资源组中必须唯一。|
    |IP 地址分配|是|**动态：**只有在将公共 IP 地址与附加到 VM 的 NIC 相关联并首次启动该 VM 时，才分配动态地址。 如果附加到 VM 的 NIC 已停止（解除分配），动态地址可能会更改。 如果 VM 重新启动或停止（但未解除分配），该地址将保持不变。 **静态：**静态地址是在创建公共 IP 地址时分配的。 即使 VM 处于停止（解除分配）状态，静态地址也不会发生更改。 仅当删除 NIC 时，才释放该地址。 创建 NIC 后，可以更改分配方法。|
    |空闲超时（分钟）|否|不依赖于客户端发送 keep-alive 消息，将 TCP 或 HTTP 连接保持打开的分钟数。|
    |DNS 名称标签|否|必须在创建该名称的 Azure 位置（所有订阅和所有客户位置）中保持唯一。 Azure 公共 DNS 服务将自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 将 *location.cloudapp.azure.com*（其中的 location 是所选的位置）追加到提供的名称后面，创建完全限定的 DNS 名称。|
    |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
    |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
    |位置|是|必须与要将公共 IP 地址关联到的资源位于同一[位置](https://azure.microsoft.com/regions)（也称为“区域”）。|

**命令**

|工具|命令|
|---|---|
|CLI|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>查看、更改公共 IP 地址的设置或删除公共 IP 地址

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中，单击要查看、更改其设置或要删除的公共 IP 地址的名称。
4. 在出现的公共 IP 地址边栏选项卡中，根据是要删除还是更改公共 IP 地址，完成以下选项之一。
    - **查看**：边栏选项卡的“概述”部分显示公共 IP 地址的主要设置，例如与之关联的网络接口（如果地址与某个网络接口关联）。
    - **删除**：若要删除公共 IP 地址，请在边栏选项卡的“概述”部分中单击“删除”。 如果该地址当前已关联到某个 IP 配置，则无法将其删除。 如果该地址当前已关联到某个配置，请单击“取消关联”，从该 IP 配置中取消关联该地址。
    - **更改**：单击“配置”。 使用本文[创建公共 IP 地址](#create)部分所述的步骤 4 中的信息更改设置。 若要将分配方法从静态更改为动态，必须先从该公开 IP 地址关联到的 IP 配置中取消关联该地址。 然后，可将分配方法更改为动态，并单击“关联”将该 IP 地址关联到相同或不同的 IP 配置，或者让它保持取消关联状态。 若要取消关联公共 IP 地址，请在“概述”部分中单击“取消关联”。

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

