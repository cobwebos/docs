---
title: 创建、更改或删除 Azure 公共 IP 地址前缀 | Microsoft Docs
description: 了解如何创建、更改或删除公共 IP 地址前缀。
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 1426bddb6e9e3e110fe5a86ed451ec40c2677e78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957481"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>创建、更改或删除公共 IP 地址前缀

了解公共 IP 地址前缀，以及如何创建、更改和删除此类地址。 公共 IP 地址前缀是基于指定的公共 IP 地址数量的连续地址范围。 地址会分配给你的订阅。 当创建公共 IP 地址资源时，可以从前缀分配一个静态公共 IP 地址，并将其关联到虚拟机、负载均衡器或其他资源，以启用 Internet 连接。 如果不熟悉公共 IP 地址前缀，请参阅[公共 IP 地址前缀概述](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>开始之前

> [!IMPORTANT]
> 公共 IP 前缀在有限区域中以公共预览版提供。 可以[在预览版中了解其含义](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 公共 IP 前缀目前在以下地区可用：美国中西部、美国西部、美国西部 2、美国中部、北欧、西欧和东南亚。 有关更新的区域列表，请访问 [Azure 更新](https://azure.microsoft.com/updates/?product=virtual-network)。

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://aka.ms/publicipprefixportal，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 AzureRm.Network PowerShell 模块 6.3.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM.Network` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.41 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

公共 IP 地址前缀会产生费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="create-a-public-ip-address-prefix"></a>创建公共 IP 地址前缀

1. 在门户左上角的顶部选择“+ 创建资源”。
2. 在“在市场中搜索”框中输入“公共 IP 地址前缀”。 当“公共 IP 地址前缀”出现在搜索结果中时，请选择它。
3. 在“公共 IP 地址前缀”下，选择“创建”。
4. 在“创建公共 IP 地址前缀”下为以下设置输入或选择值，然后选择“创建”：

   |设置|必需？|详细信息|
   |---|---|---|
   |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
   |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
   |名称|是|该名称在所选的资源组中必须唯一。|
   |区域|是|必须位于与公共 IP 地址相同的[区域](https://azure.microsoft.com/regions)，你将从该范围分配地址。 前缀目前在以下地区为预览版：美国中西部、美国西部、美国西部 2、美国中部、北欧、西欧和东南亚。|
   |前缀大小|是| 所需的前缀大小。 /28 或 16 个 IP 地址为默认值。 

**命令**


|工具|命令|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-create)|
|PowerShell|[New-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/new-azurermpublicipaddressprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>从前缀创建静态公共 IP 地址
在创建前缀后，必须从前缀创建静态 IP 地址。 为此，请执行以下步骤。

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址前缀”。 当“公共 IP 地址前缀”出现在搜索结果中时，请选择它。
2. 选择想要从中创建公共 IP 的前缀。
3. 当出现在搜索结果中，选择它，然后单击“概述”部分中的“+添加 IP 地址”。 如果未出现，请确保使用正确的预览链接： https://aka.ms/publicipprefixportal
4. 在“创建公共 IP 地址”下为以下设置输入或选择值。 由于前缀适用于标准 SKU、IPv4 和静态，因此只需提供以下信息：

   |设置|必需？|详细信息|
    |---|---|---|
    |名称|是|公共 IP 地址的名称在所选的资源组中必须唯一。|
   |空闲超时（分钟）|否|不依赖于客户端发送 keep-alive 消息，将 TCP 或 HTTP 连接保持打开的分钟数。 |
   |DNS 名称标签|否|必须在创建该名称的 Azure 区域（跨所有订阅和所有客户）中保持唯一。 Azure 会在其 DNS 中自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 会将“location.cloudapp.azure.com”（其中 location 是所选的位置）此类默认子网追加到提供的名称后面，以创建完全限定的 DNS 名称。有关详细信息，请参阅[将 Azure DNS 与 Azure 公共 IP 地址配合使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|
   |可用性区域|  否 | 选择受支持的区域时才会显示此设置。 有关支持位置的列表，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。会自动选中区域冗余，让数据路径在区域故障时具有恢复能力。 如果倾向于保证特定区域（区域故障时不具有恢复能力），可以选择一个特定区域。

## <a name="view-or-delete-a-prefix"></a>查看或删除前缀

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址前缀”。 当“公共 IP 地址前缀”出现在搜索结果中时，请选择它。
2. 选择要查看、更改其设置或从列表中删除的公共 IP 地址前缀的名称。
3. 根据是要查看、删除还是更改公共 IP 地址前缀，完成以下选项之一。
    - 视图：“概述”部分显示公共 IP 地址前缀的关键设置，如前缀。
    - 删除：若要删除公共 IP 地址前缀，请在“概述”部分中选择“删除”。 如果前缀中的地址关联到公共 IP 地址资源，必须先删除公共 IP 地址资源。 请参阅[删除公共 IP 地址](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address)。

**命令**

|工具|命令|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-list) 用于列出公共 IP 地址；[az network public-ip prefix show](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-show) 用于显示设置；[az network public-ip prefix update](/cli/azure/network/public-ip-prefix#az-network-public-ip-prefix-update) 用于更新；[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-delete) 用于删除|
|PowerShell|[Get-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/get-azurermpublicipaddressprefix) 用于检索公共 IP 地址对象并查看其设置；[Set-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/set-azurermpublicipaddressprefix) 用于更新设置；[Remove-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/remove-azurermpublicipaddressprefix) 用于删除|

## <a name="permissions"></a>权限

若要在公共 IP 地址前缀上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 操作                                                                   | 名称                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 读取公共 IP 地址前缀                                |
| Microsoft.Network/publicIPPrefixes/write                          | 创建或更新公共 IP 地址前缀                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 删除公共 IP 地址前缀                              |
|Microsoft.Network/publicIPPrefixes/join/action | 从前缀创建公共 IP 地址 |

## <a name="next-steps"></a>后续步骤

- 了解使用[公共 IP 前缀](public-ip-address-prefix.md)的方案和好处
