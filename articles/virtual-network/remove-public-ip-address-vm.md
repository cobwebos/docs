---
title: 从 Azure VM 取消关联公共 IP 地址
titlesuffix: Azure Virtual Network
description: 了解如何从 VM 取消关联公共 IP 地址
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900744"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>从 Azure VM 取消关联公共 IP 地址 

本文介绍如何从 Azure 虚拟机（VM）取消关联公共 IP 地址的操作。

可以使用[Azure 门户](#azure-portal)、Azure[命令行接口](#azure-cli)（CLI）或[PowerShell](#powershell)将公共 IP 地址与 VM 取消关联。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到或搜索要断开其公共 IP 地址的虚拟机，然后选择它。
3. 在 "VM" 页上，选择 "**概述**"，然后选择公共 IP 地址，如下图所示：

   ![选择公共 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在 "公共 IP 地址" 页上，选择 "**概述**" **，然后选择 "** 取消关联"，如下图所示：

    ![取消关联公共 IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在 "取消关联**公共 IP 地址**" 中选择 **"是"** 。

## <a name="azure-cli"></a>Azure CLI

安装[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置与你的帐户一起使用。 选择以下 CLI 命令中的 "**试用**" 按钮。 选择 "**试用" 会**调用 Cloud Shell，你可以使用登录到 Azure 帐户。

1. 如果在 Bash 中本地使用 CLI，请使用 `az login`登录到 Azure。
2. 公共 IP 地址与附加到 VM 的网络接口的 IP 配置关联。 使用[az network nic-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update)命令将公共 ip 地址与 ip 配置取消关联。 以下示例从名为 " *myVMVMNic* *" 的现有*网络接口的 IP 配置取消名为*myVMPublicIP*的公共 IP 地址，该网络接口附加到名为*MyVM*的资源组中名为*myResourceGroup*的 VM。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果你不知道连接到 VM 的网络接口的名称，请使用[az VM nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list)命令查看它们。 例如，以下命令将列出连接到名为*myResourceGroup*的资源组中名为*myVM*的 VM 的网络接口的名称：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出包含一个或多个类似于以下示例的行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上面的示例中， *myVMVMNic*是网络接口的名称。

   - 如果不知道网络接口的 IP 配置的名称，请使用[az network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list)命令来检索它们。 例如，以下命令将列出名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果不知道网络接口的公共 IP 配置的名称，请使用[az network nic IP-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show)命令检索它们。 例如，以下命令将列出名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安装[PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 选择下面的 PowerShell 命令中的 "**试用**" 按钮。 选择 "**试用" 会**调用 Cloud Shell，你可以使用登录到 Azure 帐户。

1. 如果在本地使用 PowerShell，请使用 `Connect-AzAccount`登录到 Azure。
2. 公共 IP 地址与附加到 VM 的网络接口的 IP 配置关联。 使用[AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令获取网络接口。 将 "公共 IP 地址" 值设置为 null，然后使用 " [AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) " 命令将新的 ip 配置写入网络接口。

   以下示例从附加到名为*myVM*的*VM 的网络*接口中取消名为*myVMPublicIP*的公共 IP 地址。 所有资源都在名为*myResourceGroup*的资源组中。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果你不知道连接到 VM 的网络接口的名称，请使用[new-azvm](/powershell/module/Az.Compute/Get-AzVM)命令进行查看。 例如，以下命令将列出连接到名为*myResourceGroup*的资源组中名为*myVM*的 VM 的网络接口的名称：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     输出包含一个或多个与下面的示例类似的行。 在示例输出中， *myVMVMNic*是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果不知道网络接口的 IP 配置的名称，请使用[AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface)命令检索它们。 例如，以下命令将列出名为*myResourceGroup*的资源组中名为*myVMVMNic*的网络接口的 IP 配置的名称：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     输出包含一个或多个与下面的示例类似的行。 在示例输出中， *ipconfigmyVM*是 IP 配置的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>后续步骤

- 了解如何[将公共 IP 地址关联到 VM](associate-public-ip-address-vm.md)。
