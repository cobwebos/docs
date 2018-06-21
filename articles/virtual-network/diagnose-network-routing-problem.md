---
title: 诊断 Azure 虚拟机路由问题 | Microsoft Docs
description: 了解如何通过查看虚拟机的有效路由来诊断虚拟机路由问题。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 07352a5d7c8b465440efab17c654979662a95f8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702636"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>诊断虚拟机路由问题

本文介绍如何通过查看虚拟机 (VM) 中网络接口的有效路由来诊断路由问题。 Azure 为每个虚拟网络子网创建多个默认路由。 可以通过在路由表中定义路由，然后将路由表关联到子网，来替代 Azure 的默认路由。 创建的路由、Azure 的默认路由，以及使用边界网关协议 (BGP) 通过 Azure VPN 网关（如果虚拟网络已连接到本地网络）从本地网络传播的任何路由的组合，就是子网中所有网络接口的有效路由。 如果不熟悉虚拟网络、网络接口或路由的概念，请参阅[虚拟网络概述](virtual-networks-overview.md)、[网络接口](virtual-network-network-interface.md)和[路由概述](virtual-networks-udr-overview.md)。

## <a name="scenario"></a>场景

你正在尝试连接到某个 VM，但连接失败。 若要确定为何无法连接到该 VM，可以使用 Azure [门户](#diagnose-using-azure-portal)、[PowerShell](#diagnose-using-powershell) 或 [Azure CLI](#diagnose-using-azure-cli) 查看网络接口的有效路由。

以下步骤假设有一个要查看其有效路由的现有 VM。 如果没有 VM，请先部署 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 以完成本文中的任务。 本文中的示例适用于名为 *myVM* 的 VM，其中包含名为 *myVMVMNic* 的网络接口。 VM 和网络接口在名为 *myResourceGroup*、位于“美国东部”区域的资源组中。 针对想要诊断其问题的 VM，相应地更改步骤中的值。

## <a name="diagnose-using-azure-portal"></a>使用 Azure 门户诊断

1. 使用拥有[所需权限](virtual-network-network-interface.md#permissions)的 Azure 帐户登录到 Azure [门户](https://portal.azure.com)。
2. 在 Azure 门户顶部的搜索框中，输入处于运行状态的 VM 的名称。 当 VM 名称显示在搜索结果中时，请选择它。
3. 选择“诊断并解决问题”，然后在“建议的步骤”下，选择第 7 项中的“有效路由”，如下图所示：

    ![查看有效路由](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. 下图显示了名为 **myVMVMNic** 的网络接口的有效路由：

     ![查看有效路由](./media/diagnose-network-routing-problem/effective-routes.png)

    如果已将多个网络接口附加到 VM，可以选择任一网络接口来查看其有效路由。 由于每个网络接口可能位于不同的子网中，因此，每个网络接口可能有不同的有效路由。

    在上图中所示的示例中，列出的路由是 Azure 为每个子网创建的默认路由。 你的列表至少包含这些路由，但可能还包含其他路由，具体取决于为虚拟网络启用的功能，例如，已将它对等互连到另一个虚拟网络，或通过 Azure VPN 网关连接到本地网络。 若要详细了解每个路由以及网络接口的其他路由，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md)。 如果列表中包含大量的路由，你可能会发现，选择“下载”来下载包含路由列表的 .csv 文件会更方便。

尽管上述步骤是通过 VM 查看有效路由，但也可以通过以下方式查看有效路由：
- **单个网络接口**：了解如何[查看网络接口](virtual-network-network-interface.md#view-network-interface-settings)。
- **单个路由表**：了解如何[查看路由表](manage-route-table.md#view-details-of-a-route-table)。

## <a name="diagnose-using-powershell"></a>使用 PowerShell 诊断

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，需要 *AzureRM* PowerShell 模块 6.0.1 或更高版本。 在计算机上运行 `Get-Module -ListAvailable AzureRM`，找到已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需要运行 `Login-AzureRmAccount`，以使用拥有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

使用 [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 获取网络接口的有效路由。 以下示例获取资源组 *myResourceGroup* 中名为 *myVMVMNic* 的网络接口的有效路由：

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

若要了解输出中返回的信息，请参阅[路由概述](virtual-networks-udr-overview.md)。 仅当 VM 处于运行状态时，才返回输出。 如果已将多个网络接口附加到 VM，可以查看每个网络接口的有效路由。 由于每个网络接口可能位于不同的子网中，因此，每个网络接口可能有不同的有效路由。 如果仍遇到通信问题，请参阅[其他诊断](#additional-diagnosis)和[注意事项](#considerations)。

如果不知道网络接口的名称，但知道网络接口所附加到的 VM 的名称，则运行以下命令会返回附加到 VM 的所有网络接口的 ID：

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

将会收到类似于以下示例的输出：

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

在前面的输出中，网络接口名称为 *myVMVMNic*。

## <a name="diagnose-using-azure-cli"></a>使用 Azure CLI 诊断

可以在 [Azure Cloud Shell](https://shell.azure.com/bash) 中运行以下命令，或者在计算机上运行 CLI。 本文需要 Azure CLI 2.0.32 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需要运行 `az login`，并使用拥有[所需权限](virtual-network-network-interface.md#permissions)的帐户登录到 Azure。

使用 [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) 获取网络接口的有效路由。 以下示例获取资源组 *myResourceGroup* 中名为 *myVMVMNic* 的网络接口的有效路由：

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

若要了解输出中返回的信息，请参阅[路由概述](virtual-networks-udr-overview.md)。 仅当 VM 处于运行状态时，才返回输出。 如果已将多个网络接口附加到 VM，可以查看每个网络接口的有效路由。 由于每个网络接口可能位于不同的子网中，因此，每个网络接口可能有不同的有效路由。 如果仍遇到通信问题，请参阅[其他诊断](#additional-diagnosis)和[注意事项](#considerations)。

如果不知道网络接口的名称，但知道网络接口所附加到的 VM 的名称，则运行以下命令会返回附加到 VM 的所有网络接口的 ID：

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>解决问题

解决路由问题的过程通常包括：

- 创建自定义路由来替代 Azure 的默认路由。 了解如何[添加自定义路由](manage-route-table.md#create-a-route)。
- 更改或删除可能导致路由到不需要的位置的自定义路由。 了解如何[更改](manage-route-table.md#change-a-route)或[删除](manage-route-table.md#delete-a-route)自定义路由。
- 确保包含定义的任何自定义路由的路由表已关联到网络接口所在的子网。 了解如何[将路由表关联到子网](manage-route-table.md#associate-a-route-table-to-a-subnet)。
- 确保部署的设备（例如 Azure VPN 网关或网络虚拟设备）正常运行。 使用网络观察程序的 [VPN 诊断](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能来确定 Azure VPN 网关的任何问题。

如果仍然遇到通信问题，请参阅[注意事项](#considerations)和[其他诊断](#additional-dignosis)。

## <a name="considerations"></a>注意事项

排查通信问题时，请注意以下几点：

- 路由基于定义的路由、边界网关协议 (BGP) 和系统路由之间的最长前缀匹配 (LPM)。 如果有多个路由的 LPM 匹配情况相同，则会按[路由概述](virtual-networks-udr-overview.md#how-azure-selects-a-route)中所列的顺序，根据路由的来源选择路由。 对于有效路由，只能查看基于所有可用路由匹配 LPM 的有效路由。 查看网络接口的路由评估方式可以更方便地对可能影响 VM 通信的特定路由进行故障排除。
- 如果定义了网络虚拟设备 (NVA) 的自定义路由（“虚拟设备”是下一跃点类型），请确保接收流量的 NVA 已启用 IP 转发，否则数据包会被丢弃。 详细了解如何[为网络接口启用 IP 转发](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)。 此外，NVA 中的操作系统或应用程序也必须能够转发网络流量，并且能够配置为执行此操作。
- 如果创建了 0.0.0.0/0 的路由，所有出站 Internet 流量将路由到指定的下一跃点，从而路由到 NVA 或 VPN 网关。 创建此类路由的方法通常称为强制隧道。 根据下一跃点处理流量的方式，使用此路由可能无法从 Internet 通过 RDP 或 SSH 来与 VM 建立远程连接。 符合以下条件时，可以启用强制隧道：
    - 使用站点到站点 VPN 创建下一跃点类型为“VPN 网关”的路由。 详细了解如何[配置强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - 使用站点到站点 VPN 或 ExpressRoute 线路时，通过虚拟网络网关基于 BGP 播发 0.0.0.0/0（默认路由）。 详细了解如何将 BGP 与[站点到站点 VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering) 配合使用。
- 要使虚拟网络对等互连流量正常工作，对等互连虚拟网络的前缀范围中必须存在下一跃点类型为“VNet 对等互连”的系统路由。 如果没有此类路由，并且虚拟网络对等互连链接为“已连接”：
    - 请等待几秒钟后重试。 如果这是一个新建的对等互连链接，有时需要花费较长的时间才能将路由传播到子网中的所有网络接口。 若要详细了解虚拟网络对等互连，请参阅[虚拟网络对等互连概述](virtual-network-peering-overview.md)和[管理虚拟网络对等互连](virtual-network-manage-peering.md)。
    - 网络安全组规则可能会影响通信。 有关详细信息，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-network-traffic-filter-problem.md)。
- 尽管 Azure 会将默认路由分配到每个 Azure 网络接口，但如果已将多个网络接口附加到 VM，则只会为主要网络接口或 VM 操作系统中的网关分配默认路由 (0.0.0.0/0)。 了解如何为附加到 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM 的辅助网络接口创建默认路由。 详细了解[主要和辅助网络接口](virtual-network-network-interface-vm.md#constraints)。

## <a name="additional-diagnosis"></a>其他诊断

* 若要运行快速测试来确定发往某个位置的流量的下一跃点类型，请使用 Azure 网络观察程序的[下一跃点](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能。 下一跃点告知发往指定位置的流量的下一跃点类型是什么。
* 如果没有任何路由导致 VM 的网络通信失败，则问题可能在于 VM 操作系统中运行的防火墙软件
* 如果使用[强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)通过 VPN 网关或 NVA 将流量发送到本地设备，可能无法从 Internet 连接到 VM，具体取决于为设备配置路由的方式。 确认针对设备配置的路由是将流量路由到 VM 的公共还是专用 IP 地址。
* 使用网络观察程序的[连接故障排除](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能，来确定出站通信问题的路由、筛选和 OS 内部相关原因。

## <a name="next-steps"></a>后续步骤

- 了解[路由表和路由](manage-route-table.md)的所有任务、属性和设置。
- 了解所有的[下一跃点类型、系统路由以及 Azure 如何选择路由](virtual-networks-udr-overview.md)。
