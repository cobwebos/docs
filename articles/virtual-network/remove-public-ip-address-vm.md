---
title: 将公共 IP 地址与 Azure VM 取消关联
titlesuffix: Azure Virtual Network
description: 了解如何将公共 IP 地址与 VM 取消关联
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900744"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>将公共 IP 地址与 Azure VM 取消关联 

本文介绍如何将公共 IP 地址与 Azure 虚拟机 (VM) 取消关联。

可以使用 [Azure 门户](#azure-portal)、Azure [命令行接口](#azure-cli) (CLI) 或 [PowerShell](#powershell) 将公共 IP 地址与 VM 取消关联。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 浏览或搜索要将公共 IP 地址与之取消关联的虚拟机，然后将其选中。
3. 在“VM”页中选择“概览”****，然后选择公共 IP 地址，如下图所示：

   ![选择公共 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在公共 IP 地址页中选择“概览”****，然后选择“取消关联”，如下图所示：****

    ![取消关联公共 IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在“取消关联公共 IP 地址”中，选择“是”。********

## <a name="azure-cli"></a>Azure CLI

安装[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure 云外壳。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 在随后的 CLI 命令中选择“尝试”按钮。**** 选择“尝试”会调用一个可用于登录到 Azure 帐户的 Cloud Shell。****

1. 如果在 Bash 本地使用 CLI，请使用 `az login` 登录到 Azure。
2. 公共 IP 地址将关联到 VM 上附加的网络接口的 IP 配置。 使用 [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 命令将公共 IP 地址与 IP 配置取消关联。 以下示例将公共 IP 地址 *myVMPublicIP* 与资源组 *myResourceGroup* 中现有网络接口 *myVMVMNic*（已附加到名为 *myVM* 的 VM）的 IP 配置 *ipconfigmyVM* 取消关联。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果你不知道附加到 VM 的网络接口的名称，请使用 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 命令查看名称。 例如，以下命令会列出附加到资源组 *myResourceGroup* 中 VM *myVM* 的网络接口的名称：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出中包含类似于以下示例的一个或多个行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在以上示例中，*myVMVMNic* 是网络接口的名称。

   - 如果你不知道网络接口的 IP 配置的名称，请使用 [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 命令检索名称。 例如，以下命令会列出资源组 *myResourceGroup* 中网络接口 *myVMVMNic* 的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果你不知道网络接口的公共 IP 配置的名称，请使用 [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) 命令检索名称。 例如，以下命令会列出资源组 *myResourceGroup* 中网络接口 *myVMVMNic* 的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安装[PowerShell](/powershell/azure/install-az-ps)或使用 Azure 云外壳。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 在随后的 PowerShell 命令中选择“尝试”按钮。**** 选择“尝试”会调用一个可用于登录到 Azure 帐户的 Cloud Shell。****

1. 如果在本地使用 PowerShell，请使用 `Connect-AzAccount` 登录到 Azure。
2. 公共 IP 地址将关联到 VM 上附加的网络接口的 IP 配置。 使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令获取网络接口。 将“公共 IP 地址”值设置为 null，然后使用 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 命令将新的 IP 配置写入网络接口。

   以下示例将公共 IP 地址 *myVMPublicIP* 与网络接口 *myVMVMNic*（已附加到名为 *myVM* 的 VM）取消关联。 所有资源位于名为 *myResourceGroup* 的资源组中。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果你不知道附加到 VM 的网络接口的名称，请使用 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 命令查看名称。 例如，以下命令会列出附加到资源组 *myResourceGroup* 中 VM *myVM* 的网络接口的名称：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     输出中包含类似于以下示例的一个或多个行。 在示例输出中，*myVMVMNic* 是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果你不知道网络接口的 IP 配置的名称，请使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令检索名称。 例如，以下命令会列出资源组 *myResourceGroup* 中网络接口 *myVMVMNic* 的 IP 配置的名称：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     输出中包含类似于以下示例的一个或多个行。 在示例输出中，*ipconfigmyVM* 是 IP 配置的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>后续步骤

- 了解如何[将公共 IP 地址关联到 VM](associate-public-ip-address-vm.md)。
