---
title: "Azure 公共 IP 地址 | Microsoft Docs"
description: "了解如何创建、修改和删除公共 IP 地址。"
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9e65a61b2b156611e998f266068ab5e1e306143d
ms.lasthandoff: 04/27/2017


---

# <a name="public-ip-addresses"></a>公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除公共 IP 地址。 公共 IP 地址是具有自身可配置设置的资源。 将公共 IP 地址分配到其他 Azure 资源可实现以下目的：
- 与 Azure 虚拟机 (VM)、Azure 虚拟机规模集、Azure VPN 网关和面向 Internet 的 Azure 负载均衡器等资源建立入站 Internet 连接。
- 与未经过网络地址转换 (NAT) 的 Internet 建立出站连接。 例如，VM 可与未分配公共 IP 地址，但其地址已经过 Azure 网络地址转换的 Internet 进行出站通信。 有关从 Azure 资源建立出站连接的详细信息，请阅读 [Understand outbound connections](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（了解出站连接）一文。

本文还介绍了如何使用公共 IP 地址。 本文适用于通过 Azure Resource Manager 部署模型部署的资源。 尽管可将公共 IP 地址分配给通过经典部署模型部署的资源，但这些地址的应用方式不同于通过 Resource Manager 部署的资源。 如果你不熟悉这两个模型之间的差别，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

本文的余下部分列出了完成所有公开 IP 地址相关任务的步骤。 每个部分列出了：
- 在 Azure 门户中完成任务的步骤。 若要完成这些步骤，必须登录到 [Azure 门户](http://portal.azure.com)。 如果你没有帐户，可注册一个[免费试用帐户](https://azure.microsoft.com/free)。
- 用于通过 Azure PowerShell 完成任务的命令，以及相关命令的命令参考链接。 请按照[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 一文中的步骤完成 PowerShell 的安装和配置。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 用于通过 Azure 命令行接口 (CLI) 完成任务的命令，以及相关命令的命令参考链接。 请完成[如何安装和配置 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> -h`。

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 可在一个订阅中使用的公共 IP 地址数有限制。 若要查看限制，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 

完成以下部分中的步骤创建、更改或删除公共 IP 地址资源：

## <a name="create"></a>创建公共 IP 地址

若要创建公共 IP 地址，请完成以下步骤：
1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中单击“+ 添加”。
4. 在出现的“创建公共 IP 地址”边栏选项卡中，输入或选择以下设置的值，然后单击“创建”：

    |**设置**|必需？|**详细信息**|
    |---|---|---|
    |**Name**|是|该名称在所选的资源组中必须唯一。|
    |**IP 地址分配**|是|**动态：**只有在将公共 IP 地址与附加到 VM 的 NIC 相关联并首次启动该 VM 时，才分配动态地址。 如果附加到 VM 的 NIC 已停止（解除分配），动态地址可能会更改。 如果 VM 重新启动或停止（但未解除分配），该地址将保持不变。 **静态：**静态地址是在创建公共 IP 地址时分配的。 即使 VM 处于停止（解除分配）状态，静态地址也不会发生更改。 仅当删除 NIC 时，才释放该地址。 创建 NIC 后，可以更改分配方法。|
    |**空闲超时(分钟)**|否|不依赖于客户端发送 keep-alive 消息，将 TCP 或 HTTP 连接保持打开的分钟数。|
    |**DNS 名称标签**|否|必须在创建该名称的 Azure 位置（所有订阅和所有客户位置）中保持唯一。 Azure 公共 DNS 服务将自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 将 *location.cloudapp.azure.com*（其中的 location 是所选的位置）追加到提供的名称，创建完全限定的 DNS 名称。 |
    |**订阅**|是|必须与想要将公共 IP 地址关联到的资源位于同一订阅中。|
    |**资源组**|是|可与想要将公共 IP 地址关联到的资源位于相同或不同的资源组中。|
    |**位置**|是|必须与想要将公共 IP 地址关联到的资源位于同一位置。|

|**工具**|**命令**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>更改设置或删除公共 IP 地址

若要更改或删除公共 IP 地址，请完成以下步骤：

1. 使用已分配订阅的“网络参与者”角色权限（最低权限）的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色和权限分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请单击它。
3. 在出现的“公共 IP 地址”边栏选项卡中，单击要更改其设置或要删除的公共 IP 地址的名称。
4. 在出现的公共 IP 地址边栏选项卡中，根据是要删除还是更改公共 IP 地址，完成以下选项之一。
    - **删除：**若要删除公共 IP 地址，请在该边栏选项卡的“概述”部分中单击“删除”。 如果该地址当前已关联到某个 IP 配置，则无法将其删除。 如果该地址当前已关联到某个配置，请单击“取消关联”，从该 IP 配置中取消关联该地址。
    - **更改：**单击“配置”。 使用本文[创建公共 IP 地址](#create)部分所述的步骤 4 中的信息更改设置。 若要将分配方法从静态更改为动态，必须先从该公开 IP 地址关联到的 IP 配置中取消关联该地址。 然后，可将分配方法更改为动态，并单击“关联”将该 IP 地址关联到相同或不同的 IP 配置，或者让它保持取消关联状态。 若要取消关联公共 IP 地址，请在“概述”部分中单击“取消关联”。

>[!WARNING]
>将分配方法从静态更改为动态时，将丢失已分配到公共 IP 地址的 IP 地址。 尽管 Azure 公共 DNS 服务器会保留静态或动态地址与任何 DNS 名称标签（如果已定义）之间的映射，但如果 VM 在处于停止（解除分配）状态之后启动，动态 IP 地址可能会更改。 若要防止地址更改，请分配静态 IP 地址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) 用于更新；[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) 用于删除|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) 用于更新；[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) 用于删除|

## <a name="next-steps"></a>后续步骤
创建以下 Azure 资源时分配公共 IP 地址：

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机
- [面向 Internet 的 Azure 负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 应用程序网关](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Azure VPN 网关建立站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

