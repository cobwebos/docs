---
title: 使用 PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙
description: 本文介绍如何使用 Azure PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 178daba47acaf8ad161a0634c16b046ec330abb6
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864421"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>使用 Azure PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙

此功能支持以下方案：

- **DNAT** - 可将多个标准端口实例转换为后端服务器。 例如，如果你有两个公共 IP 地址，可以转换这两个 IP 地址的 TCP 端口 3389 (RDP)。
- **SNAT** - 其他端口可用于出站 SNAT 连接，以减少 SNAT 端口耗尽的可能性。 目前，Azure 防火墙会随机选择用于建立连接的源公共 IP 地址。 如果你在网络中进行任何下游筛选，则需要允许与防火墙关联的所有公共 IP 地址。 请考虑使用[公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)来简化此配置。
 
可通过 Azure 门户、Azure PowerShell、Azure CLI、REST 和模板来使用具有多个公共 IP 地址的 Azure 防火墙。 可以部署最多包含250个公共 IP 地址的 Azure 防火墙。

以下 Azure PowerShell 示例显示如何配置、添加和删除 Azure 防火墙的公共 IP 地址。

> [!NOTE]
> 无法从 Azure 防火墙公共 IP 地址配置页中删除第一个 ipConfiguration。 若要修改 IP 地址，可以使用 Azure PowerShell。

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>创建具有两个或更多公共 IP 地址的防火墙

此示例创建一个附加到具有两个公共 IP 地址的虚拟网络 vNet** 的防火墙。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>将公共 IP 地址添加到现有防火墙

在此示例中，公共 IP 地址 azFwPublicIp1** 将附加到防火墙。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>从现有防火墙中删除公共 IP 地址

在此示例中，公共 IP 地址 azFwPublicIp1** 将与防火墙分离。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>后续步骤

* [快速入门：创建具有多个公共 IP 地址的 Azure 防火墙 - 资源管理器模板](quick-create-multiple-ip-template.md)
