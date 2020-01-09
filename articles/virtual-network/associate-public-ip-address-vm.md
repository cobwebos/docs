---
title: 将公共 IP 地址关联到虚拟机
titlesuffix: Azure Virtual Network
description: 了解如何将公共 IP 地址与虚拟机关联。
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
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647452"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>将公共 IP 地址关联到虚拟机

本文介绍如何将公共 IP 地址关联到现有的虚拟机（VM）。 如果要从 internet 连接到 VM，则 VM 必须具有与之关联的公共 IP 地址。 如果要使用公共 IP 地址创建新的 VM，可以使用[Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)、 [Azure 命令行接口（CLI）](virtual-network-deploy-static-pip-arm-cli.md)或[PowerShell](virtual-network-deploy-static-pip-arm-ps.md)来执行此操作。 公共 IP 地址会产生少许费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。 每个订阅可以使用的公共 IP 地址数有限制。 有关详细信息，请参阅[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)。

可以使用[Azure 门户](#azure-portal)、Azure[命令行接口](#azure-cli)（CLI）或[PowerShell](#powershell)将公共 IP 地址关联到 VM。

## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 浏览到或搜索要将公共 IP 地址添加到的虚拟机，然后选择它。
3. 在 "**设置**" 下，选择 "**网络**"，然后选择要向其添加公共 IP 地址的网络接口，如下图所示：

   ![选择网络接口](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公共 IP 地址与附加到 VM 的网络接口相关联。 在上图中，VM 只有一个网络接口。 如果 VM 有多个网络接口，则会显示这些接口，并选择要将公共 IP 地址关联到的网络接口。

4. 选择 " **ip 配置**"，然后选择 ip 配置，如下图所示：

   ![选择 IP 配置](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公共 IP 地址关联到网络接口的 IP 配置。 在上图中，网络接口有一个 IP 配置。 如果网络接口有多个 IP 配置，则它们将显示在列表中，并选择要将公共 IP 地址关联到的 IP 配置。

5. 选择 "**已启用**"，然后选择 " **IP 地址（*配置所需设置*）** "。 选择一个现有的公共 IP 地址，该地址将自动关闭 "**选择公共 ip 地址**" 框。 如果没有列出任何可用的公共 IP 地址，则需要创建一个。 若要了解如何操作，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 选择 "**保存**" （如下图所示），然后关闭 "IP 配置" 框。

   ![启用公共 IP 地址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 显示的公共 IP 地址与 VM 位于同一区域中。 如果在该区域中创建了多个公共 IP 地址，则所有这些地址将显示在此处。 如果有灰显，则原因是该地址已与其他资源关联。

6. 查看分配给 IP 配置的公共 IP 地址，如下图所示。 可能需要花费几秒钟的时间才能显示 IP 地址。

   ![查看分配的公共 IP 地址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 该地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域中使用的地址池列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是用于区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

7. 允许使用网络安全组中的安全规则[将网络流量发送到 VM](#allow-network-traffic-to-the-vm) 。

## <a name="azure-cli"></a>Azure CLI

安装[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择以下 CLI 命令中的 "**试用**" 按钮。 选择 "**试用" 会**调用 Cloud Shell，你可以使用登录到 Azure 帐户。

1. 如果在 Bash 中本地使用 CLI，请使用 `az login`登录到 Azure。
2. 公共 IP 地址与附加到 VM 的网络接口的 IP 配置关联。 使用[az network nic-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令将公共 ip 地址与 ip 配置相关联。 下面的示例将名为*myVMPublicIP*的现有公共 IP 地址关联到名为 " *myResourceGroup*" 的资源组中名为 " *myVMVMNic* " 的现有网络*接口的 IP*配置。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果没有现有的公共 IP 地址，请使用[az network 公共 ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)命令创建一个。 例如，以下命令在名为*myResourceGroup*的资源组中创建名为*MYVMPUBLICIP*的公共 IP 地址。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 上述命令将创建一个公共 IP 地址，其中包含你可能想要自定义的多个设置的默认值。 若要了解有关所有公共 IP 地址设置的详细信息，请参阅[创建公共 ip 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 该地址是从用于每个 Azure 区域的公共 IP 地址池分配的。 若要查看每个区域中使用的地址池列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果你不知道连接到 VM 的网络接口的名称，请使用[az VM nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令查看它们。 例如，以下命令将列出连接到名为*myResourceGroup*的资源组中名为*myVM*的 VM 的网络接口的名称：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出包含一个或多个类似于以下示例的行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上面的示例中， *myVMVMNic*是网络接口的名称。

   - 如果不知道网络接口的 IP 配置的名称，请使用[az network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令来检索它们。 例如，以下命令将列出名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 用[az vm list-IP-](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) address 命令查看分配给 IP 配置的公共 IP 地址。 以下示例显示了分配给名为*myResourceGroup*的资源组中名为*MYVM*的现有 VM 的 IP 地址。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 该地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域中使用的地址池列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是用于区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. 允许使用网络安全组中的安全规则[将网络流量发送到 VM](#allow-network-traffic-to-the-vm) 。

## <a name="powershell"></a>PowerShell

安装[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 选择下面的 PowerShell 命令中的 "**试用**" 按钮。 选择 "**试用" 会**调用 Cloud Shell，你可以使用登录到 Azure 帐户。

1. 如果在本地使用 PowerShell，请使用 `Connect-AzAccount`登录到 Azure。
2. 公共 IP 地址与附加到 VM 的网络接口的 IP 配置关联。 使用[AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork)和[AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig)命令获取网络接口所在的虚拟网络和子网。 接下来，使用[AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令获取网络接口，并使用[AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令获取现有的公共 IP 地址。 然后，使用[AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig)命令将公共 ip 地址关联到 IP 配置，并使用[AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface)命令将新的 ip 配置写入网络接口。

   下面的示例将名为*myVMPublicIP*的现有公共 IP 地址关联到名为 " *myVMVMNic* " 的现有网络*接口的 IP*配置，该名称位于名为*MyVMVNet*的虚拟网络中名为*myVMSubnet*的子网中。 所有资源都在名为*myResourceGroup*的资源组中。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果没有现有的公共 IP 地址，请使用[AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress)命令创建一个。 例如，以下命令在*eastus*区域中名为*myResourceGroup*的资源组中创建名为*myVMPublicIP*的*动态*公共 IP 地址。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 上述命令将创建一个公共 IP 地址，其中包含你可能想要自定义的多个设置的默认值。 若要了解有关所有公共 IP 地址设置的详细信息，请参阅[创建公共 ip 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 该地址是从用于每个 Azure 区域的公共 IP 地址池分配的。 若要查看每个区域中使用的地址池列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果你不知道连接到 VM 的网络接口的名称，请使用[new-azvm](/powershell/module/Az.Compute/Get-AzVM)命令进行查看。 例如，以下命令将列出连接到名为*myResourceGroup*的资源组中名为*myVM*的 VM 的网络接口的名称：

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     输出包含一个或多个与下面的示例类似的行。 在示例输出中， *myVMVMNic*是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果不知道网络接口所在的虚拟网络或子网的名称，请使用 `Get-AzNetworkInterface` 命令来查看信息。 例如，以下命令将获取名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的虚拟网络和子网信息：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     输出包含一个或多个与下面的示例类似的行。 在示例输出中， *myVMVNET*是虚拟网络的名称， *myVMSubnet*是子网的名称。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果不知道网络接口的 IP 配置的名称，请使用[AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令检索它们。 例如，以下命令将列出名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的 IP 配置的名称：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     输出包含一个或多个与下面的示例类似的行。 在示例输出中， *ipconfigmyVM*是 IP 配置的名称。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 通过[AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令查看分配给 IP 配置的公共 IP 地址。 以下示例显示了分配给名为*myResourceGroup*的资源组中名为*MYVMPUBLICIP*的公共 IP 地址的地址。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果不知道分配给 IP 配置的公共 IP 地址的名称，请运行以下命令进行获取：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   输出包含一个或多个与下面的示例类似的行。 在示例输出中， *myVMPublicIP*是分配给 IP 配置的公共 IP 地址的名称。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 该地址是从每个 Azure 区域中使用的地址池分配的。 若要查看每个区域中使用的地址池列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是用于区域的池中的任何地址。 如果需要从区域中的特定池分配地址，请使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. 允许使用网络安全组中的安全规则[将网络流量发送到 VM](#allow-network-traffic-to-the-vm) 。

## <a name="allow-network-traffic-to-the-vm"></a>允许到 VM 的网络流量

在从 internet 连接到公共 IP 地址之前，请确保已在可能与网络接口关联的任何网络安全组、网络接口所在的子网或两者中打开所需的端口。 尽管安全组将流量筛选到网络接口的专用 IP 地址，但一旦入站 internet 流量到达公共 ip 地址，Azure 会将公用地址转换为专用 IP 地址，因此，如果网络安全组阻止通信流与公共 IP 地址的通信失败。 可以使用[门户](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、 [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)或[PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)查看网络接口及其子网的有效安全规则。

## <a name="next-steps"></a>后续步骤

允许使用网络安全组将入站 internet 流量发送到 VM。 若要了解如何创建网络安全组，请参阅[使用网络安全组](manage-network-security-group.md#work-with-network-security-groups)。 若要了解有关网络安全组的详细信息，请参阅[安全组](security-overview.md)。
