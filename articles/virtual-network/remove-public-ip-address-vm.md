---
title: 将公共 IP 地址与 Azure VM 取消关联
titlesuffix: Azure Virtual Network
description: 了解如何将公共 IP 地址与 VM 取消关联
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: b171699a0c578b3761e58f6e0e977199369864a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84709957"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>将公共 IP 地址与 Azure VM 取消关联 

本文介绍如何将公共 IP 地址与 Azure 虚拟机 (VM) 取消关联。

可以使用 [Azure 门户](#azure-portal)、Azure [命令行接口](#azure-cli) (CLI) 或 [PowerShell](#powershell) 将公共 IP 地址与 VM 取消关联。

## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 浏览或搜索要将公共 IP 地址与之取消关联的虚拟机，然后将其选中。
3. 在 VM 页中选择“概述”，然后选择公共 IP 地址，如下图所示：

   ![选择公共 IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 在公共 IP 地址页中选择“概述”，然后选择“取消关联”，如下图所示：

    ![取消关联公共 IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 在“取消关联公共 IP 地址”中，选择“是”。

## <a name="azure-cli"></a>Azure CLI

安装 [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 在随后的 CLI 命令中选择“试用”按钮。 选择“试用”会调用可用于登录到 Azure 帐户的 Cloud Shell。

1. 如果在 Bash 本地使用 CLI，请使用 `az login` 登录到 Azure。
2. 公共 IP 地址与连接到 VM 的网络接口的 IP 配置相关联。 使用 [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 命令将公共 IP 地址与 IP 配置取消关联。 以下示例将名为 myVMPublicIP 的公共 IP 地址与名为 myVMVMNic 的现有网络接口（已连接到名为 myResourceGroup 的资源组中名为 myVM 的 VM）的名为 ipconfigmyVM 的 IP 配置取消关联。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   如果不知道连接到 VM 的网络接口的名称，请使用 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 命令进行查看。 例如，以下命令列出了连接到名为 myResourceGroup 的资源组中名为 myVM 的 VM 的网络接口的名称：

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     输出包括一个或多个类似以下示例的行：
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     在上一个示例中，myVMVMNic 是网络接口的名称。

   - 如果不知道网络接口的 IP 配置的名称，请使用 [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 命令进行检索。 例如，以下命令列出了名为 myResourceGroup 的资源组中名为 myVMVMNic 的网络接口的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 如果不知道网络接口的公共 IP 配置的名称，请使用 [az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) 命令进行检索。 例如，以下命令列出了名为 myResourceGroup 的资源组中名为 myVMVMNic 的网络接口的公共 IP 配置的名称：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

安装 [PowerShell](/powershell/azure/install-az-ps)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 shell。 它预安装有 PowerShell，并配置为与帐户一起使用。 在随后的 PowerShell 命令中选择“试用”按钮。 选择“试用”会调用可用于登录到 Azure 帐户的 Cloud Shell。

1. 如果在本地使用 PowerShell，请使用 `Connect-AzAccount` 登录到 Azure。
2. 公共 IP 地址与连接到 VM 的网络接口的 IP 配置相关联。 使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令获取网络接口。 将公共 IP 地址值设置为 null，然后使用 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 命令将新 IP 配置写入网络接口。

   以下示例将名为 myVMPublicIP 的公共 IP 地址与连接到名为 myVM 的 VM 的名为 myVMVMNic 的网络接口取消关联。 所有资源都位于名为 myResourceGroup 的资源组中。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - 如果不知道连接到 VM 的网络接口的名称，请使用 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 命令进行查看。 例如，以下命令列出了连接到名为 myResourceGroup 的资源组中名为 myVM 的 VM 的网络接口的名称：

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     输出包括一个或多个类似以下示例的行。 在示例输出中，myVMVMNic 是网络接口的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 如果不知道网络接口的 IP 配置的名称，请使用 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 命令进行检索。 例如，以下命令列出了名为 myResourceGroup 的资源组中名为 myVMVMNic 的网络接口的 IP 配置的名称：

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     输出包括一个或多个类似以下示例的行。 在示例输出中，ipconfigmyVM 是 IP 配置的名称。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>后续步骤

- 了解如何[将公共 IP 地址关联到 VM](associate-public-ip-address-vm.md)。
