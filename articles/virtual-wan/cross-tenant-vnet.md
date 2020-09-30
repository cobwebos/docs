---
title: 将跨租户 Vnet 连接到中心： PowerShell
titleSuffix: Azure Virtual WAN
description: 本文介绍如何使用 PowerShell 将跨租户 Vnet 连接到虚拟中心。
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 881f955014032d18fec447784a879fbf4f0e24fa
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571318"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>将跨租户 Vnet 连接到虚拟 Wan 集线器

本文可帮助你使用虚拟 WAN 将 VNet 连接到不同租户中的虚拟中心。 如果客户端工作负荷必须连接到同一网络，但在不同租户上，则此体系结构非常有用。 例如，如下图所示，可以将非 Contoso VNet (远程租户) 连接到 (父租户) 的 Contoso 虚拟中心。

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="设置路由配置" :::

在本文中，学习如何：

* 添加另一个租户作为你的 Azure 订阅上的参与者。
* 将跨租户 VNet 连接到虚拟中心。

此配置的步骤使用 Azure 门户和 PowerShell 的组合来执行。 但是，此功能本身仅适用于 PowerShell 和 CLI。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>先决条件

若要使用本文中的步骤，你必须已在环境中设置以下配置：

* 父订阅中的虚拟 WAN 和虚拟中心。
* 在不同 (远程) 租户的订阅中配置的虚拟网络。
* 请确保远程租户中的 VNet 地址空间不与任何其他已连接到父虚拟中心的 Vnet 中的任何其他地址空间重叠。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>分配权限

为了使与虚拟中心的父订阅能够修改和访问远程租户中的虚拟网络，需要从远程租户订阅向父订阅分配 **参与者** 权限。

1. 将 " **参与者** " 角色分配添加到 (具有虚拟 WAN 中心) 的父帐户。 可以使用 PowerShell 或 Azure 门户分配此角色。 有关步骤，请参阅下面的 **添加或删除角色分配** 项目：

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [门户](../role-based-access-control/role-assignments-portal.md)

1. 接下来，将远程租户订阅和父租户订阅添加到 PowerShell 的当前会话。 运行以下命令。 如果已登录到父项，则只需运行远程租户的命令。

   ```azurepowershell-interactive
   Add-AzAccount “xxxxx-b34a-4df9-9451-4402dcaecc5b”
   ```

1. 验证角色分配是否成功，方法是使用父凭据登录 Azure PowerShell，并运行以下命令：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 如果权限已成功传播到父项并已添加到会话中，则该远程租户所拥有的订阅将显示在该命令的输出中。

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>将 VNet 连接到中心

在以下步骤中，将虚拟网络链接到虚拟中心时，会在两个订阅的上下文之间进行切换。 替换示例值，以反映自己的环境。

1. 运行以下命令，确保处于远程帐户的上下文中：

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId “[remote ID]”
   ```

1. 创建一个本地变量，用于存储要连接到中心的虚拟网络的元数据。

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[v-net name]" -ResourceGroupName "[resource group name]"
   ```

1. 切换回父帐户。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId “[parent ID]”
   ```

1. 将 VNet 连接到中心。

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[Parent Resource Group Name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. 可以在 PowerShell 或 Azure 门户中查看新连接。

   * **PowerShell：** 如果连接已成功形成，则新建立的连接中的元数据将显示在 PowerShell 控制台中。
   * **Azure 门户：** 导航到虚拟中心、 **连接 > 虚拟网络连接**。 可以查看指向连接的指针。 若要查看实际资源，你将需要适当的权限。
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>有关

* 验证 $remote 前面 [部分](#connect) (中的元数据) 与 Azure 门户中的信息匹配。
* 可以使用远程租户资源组的 IAM 设置或 (AzSubscription) Azure PowerShell 命令来验证权限。
* 请确保在资源组的名称或任何其他特定于环境的变量的名称两侧加上引号 (例如。 ) "VirtualHub1" 或 "VirtualNetwork1"。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅：

* 虚拟 WAN [常见问题](virtual-wan-faq.md)
