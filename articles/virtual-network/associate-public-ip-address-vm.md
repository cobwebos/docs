---
title: 将公共 IP 地址关联到虚拟机
titlesuffix: Azure Virtual Network
description: 了解如何将公共 IP 地址关联到虚拟机。
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 1b201957a33acd609eed8a2373c8201bdefe9d7d
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "64691986"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>将公共 IP 地址关联到虚拟机

本文介绍如何将公共 IP 地址关联到现有的虚拟机 (VM)。 若要从 Internet 连接到某个 VM，该 VM 必须有关联的公共 IP 地址。 若要使用公共 IP 地址创建新的 VM，可以使用 [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)、[Azure 命令行接口 (CLI)](virtual-network-deploy-static-pip-arm-cli.md) 或 [PowerShell](virtual-network-deploy-static-pip-arm-ps.md) 来完成此操作。 公共 IP 地址会产生少许费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。 可为每个订阅使用的公共 IP 地址数有限制。 有关详细信息，请参阅[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)。

可以使用 [Azure 门户](#azure-portal)、Azure [命令行接口](#azure-cli) (CLI) 或 [PowerShell](#powershell) 将公共 IP 地址关联到 VM。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览或搜索要将公共 IP 地址添加到的虚拟机，然后将其选中。
3. 在“设置”下选择“网络”，然后选择要将公共 IP 地址添加到的网络接口，如下图所示：  

   ![选择网络接口](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公共 IP 地址将关联到 VM 上附加的网络接口。 上图中的 VM 只有一个网络接口。 如果 VM 有多个网络接口，它们都会显示，你需要选择要将公共 IP 地址关联到的网络接口。

4. 选择“IP 配置”，然后选择一种 IP 配置，如下图所示： 

   ![选择 IP 配置](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公共 IP 地址将关联到网络接口的 IP 配置。 上图中的网络接口只有一种 IP 配置。 如果网络接口有多种 IP 配置，它们都会出现在列表中，你需要选择要将公共 IP 地址关联到的 IP 配置。

5. 依次选择“已启用”、“IP 地址(配置所需的设置)”。    选择一个现有的公共 IP 地址，此时会自动关闭“选择公共 IP 地址”框。  如果未列出任何可用的公共 IP 地址，则需要创建一个。 若要了解如何创建，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 如下图所示选择“保存”，然后关闭 IP 配置框。 

   ![启用公共 IP 地址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 显示的公共 IP 地址是 VM 所在的同一区域中的 IP 地址。 如果在该区域中创建了多个公共 IP 地址时，所有 IP 地址都会显示在此处。 如果有任何 IP 地址灰显，原因是该地址已关联到不同的资源。

6. 查看分配给 IP 配置的公共 IP 地址，如下图所示。 IP 地址可能需要在几秒钟后才会显示。

   ![查看分配的公共 IP 地址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可能是用于该区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

7. 使用网络安全组中的安全规则[允许将网络流量发往 VM](#allow-network-traffic-to-the-vm)。

## <a name="azure-cli"></a>Azure CLI

安装[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 在随后的 CLI 命令中选择“尝试”按钮。  选择“尝试”会调用一个可用于登录到 Azure 帐户的 Cloud Shell。 

1. 如果在 Bash 本地使用 CLI，请使用 `az login` 登录到 Azure。
2. 公共 IP 地址将关联到 VM 上附加的网络接口的 IP 配置。 使用 [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 命令将公共 IP 地址关联到 IP 配置。 以下示例将现有公共 IP 地址 *myVMPublicIP* 关联到资源组 *myResourceGroup* 中现有网络接口 *myVMVMNic* 的 IP 配置 *ipconfigmyVM*。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果没有现有的公共 IP 地址，请使用 [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 命令创建一个。 例如，以下命令在名为 *myResourceGroup* 的资源组中创建名为 *myVMPublicIP* 的公共 IP 地址。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 以上命令使用你可能想要自定义的多个设置的默认值创建一个公共 IP 地址。 若要详细了解所有的公共 IP 地址设置，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 地址是从每个 Azure 区域使用的公共 IP 地址池分配的。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果你不知道附加到 VM 的网络接口的名称，请使用 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 命令查看名称。 例如，以下命令会列出附加到资源组 *myResourceGroup* 中 VM *myVM* 的网络接口的名称：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出中包含类似于以下示例的一个或多个行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在以上示例中，*myVMVMNic* 是网络接口的名称。

   - 如果你不知道网络接口的 IP 配置的名称，请使用 [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 命令检索名称。 例如，以下命令会列出资源组 *myResourceGroup* 中网络接口 *myVMVMNic* 的 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 使用 [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 命令查看分配到 IP 配置的公共 IP 地址。 以下示例显示分配到资源组 *myResourceGroup* 中现有 VM *myVM* 的 IP 地址。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可能是用于该区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. 使用网络安全组中的安全规则[允许将网络流量发往 VM](#allow-network-traffic-to-the-vm)。

## <a name="powershell"></a>PowerShell

安装[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 在随后的 PowerShell 命令中选择“尝试”按钮。  选择“尝试”会调用一个可用于登录到 Azure 帐户的 Cloud Shell。 

1. 如果在本地使用 PowerShell，请使用 `Connect-AzAccount` 登录到 Azure。
2. 公共 IP 地址将关联到 VM 上附加的网络接口的 IP 配置。 使用 [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) 和 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) 命令获取网络接口所在的虚拟网络和子网。 接下来，使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令获取网络接口，并使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 命令获取现有的公共 IP 地址。 然后使用 [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) 命令将公共 IP 地址关联到 IP 配置，并使用 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 命令将新 IP 配置写入到网络接口。

   以下示例将现有公共 IP 地址 *myVMPublicIP* 关联到现有网络接口 *myVMVMNic* 的 IP 配置 *ipconfigmyVM*，该网络接口位于虚拟网络 *myVMVNet* 的子网 *myVMSubnet* 中。 所有资源位于名为 *myResourceGroup* 的资源组中。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果没有现有的公共 IP 地址，请使用 [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) 命令创建一个。 例如，以下命令将创建*动态*名为的公共 IP 地址*myVMPublicIP*资源组中名为*myResourceGroup*中*eastus*区域。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 以上命令使用你可能想要自定义的多个设置的默认值创建一个公共 IP 地址。 若要详细了解所有的公共 IP 地址设置，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 地址是从每个 Azure 区域使用的公共 IP 地址池分配的。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果你不知道附加到 VM 的网络接口的名称，请使用 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 命令查看名称。 例如，以下命令会列出附加到资源组 *myResourceGroup* 中 VM *myVM* 的网络接口的名称：

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     输出中包含类似于以下示例的一个或多个行。 在示例输出中，*myVMVMNic* 是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果你不知道网络接口所在的虚拟网络或子网的名称，请使用 `Get-AzNetworkInterface` 命令查看该信息。 例如，以下命令获取名为 *myResourceGroup* 的资源组中名为 *myVMVMNic* 的网络接口的虚拟网络和子网信息：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     输出中包含类似于以下示例的一个或多个行。 在示例输出中，*myVMVNET* 是虚拟网络的名称，*myVMSubnet* 是子网的名称。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果你不知道网络接口的 IP 配置的名称，请使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令检索名称。 例如，以下命令会列出资源组 *myResourceGroup* 中网络接口 *myVMVMNic* 的 IP 配置的名称：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     输出中包含类似于以下示例的一个或多个行。 在示例输出中，*ipconfigmyVM* 是 IP 配置的名称。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 命令查看分配到 IP 配置的公共 IP 地址。 以下示例显示分配到资源组 *myResourceGroup* 中公共 IP 地址 *myVMPublicIP* 的地址。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果你不知道分配到 IP 配置的公共 IP 地址的名称，请运行以下命令获取该名称：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   输出中包含类似于以下示例的一个或多个行。 在示例输出中，*myVMPublicIP* 是分配到 IP 配置的公共 IP 地址的名称。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可能是用于该区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. 使用网络安全组中的安全规则[允许将网络流量发往 VM](#allow-network-traffic-to-the-vm)。

## <a name="allow-network-traffic-to-the-vm"></a>允许将网络流量发往 VM

在从 Internet 连接到公共 IP 地址之前，请确保在可能已关联到网络接口的任何网络安全组和/或网络接口所在的子网中打开所需的端口。 尽管安全组会筛选发往网络接口专用 IP 地址的流量，但一旦入站 Internet 流量抵达公共 IP 地址，Azure 就会将公共地址转换成专用 IP 地址，因此，如果网络安全组阻止流量流，则与公共 IP 地址的通信就会失败。 可以使用[门户](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、[CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) 或 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell) 查看网络接口及其子网的有效安全规则。

## <a name="next-steps"></a>后续步骤

使用网络安全组允许将入站 Internet 流量发往 VM。 若要了解如何创建网络安全组，请参阅[使用网络安全组](manage-network-security-group.md#work-with-network-security-groups)。 若要详细了解网络安全组，请参阅[安全组](security-overview.md)。
