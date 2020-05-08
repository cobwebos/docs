---
title: 创建具有静态公共 IP 地址的 VM - PowerShell | Microsoft Docs
description: 了解如何使用 PowerShell 创建具有静态公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: a7b1e7471f7850f71596317c7e2c38db367bfd3a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790076"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>使用 PowerShell 创建具有静态公共 IP 地址的虚拟机

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以创建具有静态公共 IP 地址的虚拟机。 使用公共 IP 地址可以通过 Internet 来与虚拟机通信。 分配静态公共 IP 地址而非动态地址可以确保地址永远不会改变。 详细了解[静态公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要将分配给现有虚拟机的公共 IP 地址从动态更改为静态，或者要使用专用 IP 地址，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。 公共 IP 地址会产生[少许费用](https://azure.microsoft.com/pricing/details/ip-addresses)，可为每个订阅使用的公共 IP 地址数有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以从本地计算机或通过使用 Azure Cloud Shell 完成以下步骤。 若要使用本地计算机，请确保[安装了 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，请在接下来出现的任何命令框的右上角选择“试用”。**** Cloud Shell 会将你登录到 Azure。

1. 如果使用 Cloud Shell，请跳到步骤 2。 打开命令会话并使用 `Connect-AzAccount` 登录到 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. 使用 [New-AzVM](/powershell/module/az.Compute/New-azVM) 命令创建虚拟机。 `-AllocationMethod "Static"` 选项向虚拟机分配静态公共 IP 地址。 以下示例使用名为 *myPublicIpAddress* 的静态、基本 SKU 公共 IP 地址创建 Windows Server 虚拟机。 出现提示时，提供要用作虚拟机的登录凭据的用户名和密码：

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   如果公共 IP 地址必须是标准 SKU，则必须在单独的步骤中[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)、[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)、[向网络接口分配公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)，然后[使用网络接口创建虚拟机](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)。 详细了解[公共 IP 地址 SKU](virtual-network-ip-addresses-overview-arm.md#sku)。 如果虚拟机将添加到公共 Azure 负载均衡器的后端池，则虚拟机公共 IP 地址的 SKU 必须与负载均衡器的公共 IP 地址的 SKU 相匹配。 有关详细信息，请参阅 [Azure 负载均衡器](../load-balancer/skus.md)。

4. 使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 查看分配的公共 IP 地址并确认它创建为静态地址：

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure 从你在其中创建虚拟机的区域使用的地址中分配了一个公共 IP 地址。 对于 Azure [公有](https://www.microsoft.com/download/details.aspx?id=56519)云、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)云、[中国](https://www.microsoft.com/download/details.aspx?id=57062)云和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云，可以下载范围（前缀）的列表。

> [!WARNING]
> 不要修改虚拟机操作系统中的 IP 地址设置。 操作系统不知道 Azure 公共 IP 地址。 虽然可以向操作系统添加专用 IP 地址设置，但除非必要，否则我们建议不要这样做，而只能阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之后才执行此操作。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)
- 详细了解[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)
- 详细了解如何创建 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机
