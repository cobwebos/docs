---
title: 将关联到虚拟机的公共 IP 地址
titlesuffix: Azure Virtual Network
description: 了解如何将关联到虚拟机的公共 IP 地址。
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: ce573ff8fe61f2e1d4c88963e0f21fc9402776e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083209"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>将关联到虚拟机的公共 IP 地址

在本文中，您将了解如何将关联到现有的虚拟机 (VM) 的公共 IP 地址。 如果你想要从 internet 连接到 VM，VM 必须具有与之关联的公共 IP 地址。 如果你想要创建新的 VM 具有公共 IP 地址，你可以使用来完成[Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)，则[Azure 命令行接口 (CLI)](virtual-network-deploy-static-pip-arm-cli.md)，或[PowerShell](virtual-network-deploy-static-pip-arm-ps.md)。 公共 IP 地址会产生少许费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。 没有为每个订阅可以使用的公共 IP 地址数限制。 有关详细信息，请参阅[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits-1)。

可以使用[Azure 门户](#azure-portal)，Azure[命令行界面](#azure-cli)(CLI) 或[PowerShell](#powershell)要关联到 VM 的公共 IP 地址。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到或搜索你想要添加到的公共 IP 地址，然后选择该虚拟机。
3. 下**设置**，选择**网络**，然后选择你想要向其添加公共 IP 地址，如下图中所示的网络接口：

   ![选择网络接口](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 公共 IP 地址将关联到网络接口附加到 VM。 在上一张图片，VM 只有一个网络接口。 如果 VM 有多个网络接口，它们都会显示，并选择你想要关联到的公共 IP 地址的网络接口。

4. 选择**IP 配置**，然后选择 IP 配置，如下图中所示：

   ![选择 IP 配置](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 公共 IP 地址将关联到网络接口的 IP 配置。 在上一图中，网络接口具有一个 IP 配置。 如果网络接口有多个 IP 配置，它们将所有出现在列表中，并选择你想要关联到的公共 IP 地址的 IP 配置。

5. 选择**已启用**，然后选择**IP 地址 (*配置所需设置*)**。 选择一个现有的公共 IP 地址，这会自动关闭**选择公共 IP 地址**框。 如果没有列出任何可用的公共 IP 地址，需要创建一个。 若要了解如何操作，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 选择**保存**，之后，然后关闭 IP 配置框中的图片中所示。

   ![启用公共 IP 地址](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 显示的公共 IP 地址是与 VM 位于同一区域中存在。 如果有多个区域中创建的公共 IP 地址时，所有将显示在此处。 如果任何灰显，它是因为该地址已关联到不同的资源。

6. 查看分配给 IP 配置的公共 IP 地址，如下图所示。 可能需要几秒钟的 IP 地址，才会显示。

   ![查看已分配公共 IP 地址](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 从每个 Azure 区域中使用的地址池中分配的地址。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是任何地址中使用的区域的池。 如果你需要从区域中的特定池中分配的地址，使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

7. [允许到 VM 的网络流量](#allow-network-traffic-to-the-vm)使用网络安全组中的安全规则。

## <a name="azure-cli"></a>Azure CLI

安装[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择**试试**CLI 中的按钮命令后面。 选择**试试**调用可以登录到 Azure 帐户的 Cloud Shell。

1. 如果在 Bash 中本地使用 CLI，登录到 Azure 中使用`az login`。
2. 公共 IP 地址将关联到网络接口附加到 VM 的 IP 配置。 使用[az 网络 nic ip 配置更新](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令，将关联到某个 IP 配置的公共 IP 地址。 下面的示例将名为现有公共 IP 地址相关联*myVMPublicIP*到名为的 IP 配置*ipconfigmyVM*现有网络接口的名为*myVMVMNic*存在于名为的资源组*myResourceGroup*。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 如果没有现有的公共 IP 地址，使用[az 网络公共 ip 创建](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)命令创建一个。 例如，以下命令创建名为的公共 IP 地址*myVMPublicIP*资源组中名为*myResourceGroup*。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 前一个命令使用多个要自定义的设置的默认值创建一个公共 IP 地址。 若要了解有关所有公共 IP 地址设置的详细信息，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 用于每个 Azure 区域的公共 IP 地址池中分配的地址。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果不知道网络接口附加到 VM 的名称，使用[az vm nic 列表](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令以查看它们。 例如，以下命令列出了附加到名为 VM 网络接口的名称*myVM*资源组中名为*myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出包括类似于下面的示例的一个或多个行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上一示例中， *myVMVMNic*是网络接口的名称。

   - 如果不知道网络接口 IP 配置的名称，使用[az 网络 nic ip 配置列表](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令，检索它们。 例如，以下命令将列出名为网络接口 IP 配置的名称*myVMVMNic*资源组中名为*myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 查看分配的 IP 配置进行的公共 IP 地址[az vm 列表 ip 地址](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses)命令。 下面的示例演示名为分配到现有 VM 的 IP 地址*myVM*资源组中名为*myResourceGroup*。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 从每个 Azure 区域中使用的地址池中分配的地址。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是任何地址中使用的区域的池。 如果你需要从区域中的特定池中分配的地址，使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. [允许到 VM 的网络流量](#allow-network-traffic-to-the-vm)使用网络安全组中的安全规则。

## <a name="powershell"></a>PowerShell

安装[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 选择**试试**在 PowerShell 中的按钮命令后面。 选择**试试**调用可以登录到 Azure 帐户的 Cloud Shell。

1. 如果在本地使用 PowerShell，登录到 Azure 中使用`Connect-AzAccount`。
2. 公共 IP 地址将关联到网络接口附加到 VM 的 IP 配置。 使用[Get AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork)并[Get AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig)命令来获取虚拟网络和网络接口所在子网。 接下来，使用[Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令来获取网络接口和[Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令来获取现有的公共 IP 地址。 然后，使用[集 AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig)命令，将关联的 IP 配置的公共 IP 地址和[集 AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface)命令写入到的新 IP 配置网络接口。

   下面的示例将名为现有公共 IP 地址相关联*myVMPublicIP*到名为的 IP 配置*ipconfigmyVM*现有网络接口的名为*myVMVMNic*存在于名为的子网*myVMSubnet*中名为的虚拟网络*myVMVNet*。 所有资源都都将位于名为的资源组*myResourceGroup*。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 如果没有现有的公共 IP 地址，使用[新建 AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress)命令创建一个。 例如，以下命令将创建*动态*名为的公共 IP 地址*myVMPublicIP*资源组中名为*myResourceGroup*中*eastus*区域。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 前一个命令使用多个要自定义的设置的默认值创建一个公共 IP 地址。 若要了解有关所有公共 IP 地址设置的详细信息，请参阅[创建公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。 用于每个 Azure 区域的公共 IP 地址池中分配的地址。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

   - 如果不知道网络接口附加到 VM 的名称，使用[Get AzVM](/powershell/module/Az.Compute/Get-AzVM)命令以查看它们。 例如，以下命令列出了附加到名为 VM 网络接口的名称*myVM*资源组中名为*myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     输出包括类似于下面的示例中的一个或多个行。 在示例输出中， *myVMVMNic*是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果不知道网络接口所在子网的虚拟网络的名称，使用`Get-AzNetworkInterface`命令来查看的信息。 例如，以下命令将获取名为网络接口的虚拟网络和子网信息*myVMVMNic*资源组中名为*myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     输出包括类似于下面的示例中的一个或多个行。 在示例输出中， *myVMVNET*是虚拟网络的名称和*myVMSubnet*是子网的名称。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 如果不知道网络接口 IP 配置的名称，使用[Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令，检索它们。 例如，以下命令将列出名为网络接口 IP 配置的名称*myVMVMNic*资源组中名为*myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     输出包括类似于下面的示例中的一个或多个行。 在示例输出中， *ipconfigmyVM*是 IP 配置的名称。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 查看分配的 IP 配置进行的公共 IP 地址[Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)命令。 下面的示例显示了分配给名为的公共 IP 地址的地址*myVMPublicIP*资源组中名为*myResourceGroup*。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   如果不知道分配给 IP 配置的公共 IP 地址的名称，运行以下命令以获取它：

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   输出包括类似于下面的示例中的一个或多个行。 在示例输出中， *myVMPublicIP*是分配给 IP 配置的公共 IP 地址的名称。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 从每个 Azure 区域中使用的地址池中分配的地址。 若要查看每个区域使用的地址池的列表，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 分配的地址可以是任何地址中使用的区域的池。 如果你需要从区域中的特定池中分配的地址，使用[公共 IP 地址前缀](public-ip-address-prefix.md)。

4. [允许到 VM 的网络流量](#allow-network-traffic-to-the-vm)使用网络安全组中的安全规则。

## <a name="allow-network-traffic-to-the-vm"></a>允许到 VM 的网络流量

可以从 internet 连接到公共 IP 地址之前，请确保您有任何可能已关联到网络接口、 所在子网的网络接口，或两者的网络安全组中打开所需的端口。 尽管安全组筛选到网络接口的入站的 internet 流量在到达 Azure 的公共 IP 地址后的专用 IP 地址的流量转换到的专用 IP 的公共地址解决问题，因此，如果网络安全组会阻碍通信流，与公共 IP 地址的通信失败。 您可以查看网络接口和其子网使用的有效安全规则[门户](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)， [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)，或[PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)。

## <a name="next-steps"></a>后续步骤

与网络安全组允许你的 VM 的入站的 internet 流量。 若要了解如何创建网络安全组，请参阅[使用网络安全组](manage-network-security-group.md#work-with-network-security-groups)。 若要了解有关网络安全组的详细信息，请参阅[安全组](security-overview.md)。