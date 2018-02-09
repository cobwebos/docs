---
title: "创建 Azure 虚拟网络对等互连 - 不同部署模型 - 相同订阅 | Microsoft Docs"
description: "了解如何在通过相同 Azure 订阅中的不同 Azure 部署模型创建的虚拟网络间创建虚拟网络对等互连。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 033d696f0d219fef66b4acd523f28a35afcf9929
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>创建虚拟网络对等互连 - 不同部署模型，相同订阅 

本文介绍如何在通过不同部署模型创建的虚拟网络间创建虚拟网络对等互连。 这两个虚拟网络位于同一订阅。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。 

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 单击下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[均为 Resource Manager 模型](virtual-network-create-peering.md) |相同|
|[均为 Resource Manager 模型](create-peering-different-subscriptions.md) |不同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models-subscriptions.md) |不同|

不能在通过经典部署模型部署的两个虚拟网络之间创建对等互连。 如需连接两个通过经典部署模型创建的虚拟网络，可使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来连接它们。 

本教程将在同一区域中的虚拟网络之间建立对等互连。 在不同区域的虚拟网络之间建立对等互连的功能目前处于预览版状态。 首先完成[注册全局虚拟网络对等互连](#register)中的步骤，然后再尝试在不同区域中的虚拟网络之间建立对等互连，否则对等互连的建立将失败。 使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)在不同区域连接虚拟网络的功能现已公开发布且无需注册。

可以使用 [Azure 门户](#portal)、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell)、或 [Azure 资源管理器模板](#template)创建虚拟网络对等互连。 单击以前的任何工具链接直接转到使用所选工具创建虚拟网络对等互连的步骤。

## <a name="create-peering---azure-portal"></a>创建对等互连 - Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
2. 依次单击“+ 新建”、“网络”、“虚拟网络”。
3. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：
    - **名称**：*myVnet1*
    - **地址空间**：*10.0.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.0.0.0/24*
    - **订阅**：选择你的订阅
    - **资源组**：选择“新建”并输入 *myResourceGroup*
    - **位置**：美国东部
4. 单击“+ 新建”。 在“搜索 Marketplace”框中，键入“虚拟网络”。 单击搜索结果中出现的“虚拟网络”。 
5. 在“虚拟网络”边栏选项卡的“选择部署模型”框中选择“经典”，然后单击“创建”。
6. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：
    - **名称**：*myVnet2*
    - **地址空间**：*10.1.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.1.0.0/24*
    - **订阅**：选择你的订阅
    - **资源组**：选择“使用现有”和“myResourceGroup”
    - **位置**：美国东部
7. 在门户顶部的“搜索资源”框中键入 *myResourceGroup*。 当“myResourceGroup”出现在搜索结果中时，请单击它。 随后将显示 **myresourcegroup** 资源组的边栏选项卡。 该资源组包含前面步骤中创建的两个虚拟网络。
8. 单击“myVNet1”。
9. 在显示的“myVnet1”边栏选项卡中，单击左侧垂直选项列表中的“对等互连”。
10. 在显示的“myVnet1 - 对等互连”边栏选项卡中，单击“+ 添加”
11. 在显示的“添加对等互连”边栏选项卡中，输入或选择以下选项，然后单击“确定”：
     - **名称**：*myVnet1ToMyVnet2*
     - **虚拟网络部署模型**：选择“经典”。 
     - **订阅**：选择你的订阅
     - **虚拟网络**：单击“选择虚拟网络”，然后单击“myVnet2”。
     - **允许虚拟网络访问：**确保选择“已启用”。
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
12. 在上一步骤中单击“确定”后，“添加对等互连”边栏选项卡将会关闭，并再次出现“myVnet1 - 对等互连”边栏选项卡。 几秒钟后，创建的对等互连将显示在该边栏选项卡中。 创建的 myVnet1ToMyVnet2 对等互连的“对等互连状态”列中列出了“已连接”。

    现已建立对等互连。 在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
13. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
14. **可选：**若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>创建对等互连 - Azure CLI

1. [安装](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 1.0，创建虚拟网络（经典）。
2. 使用 `azure login` 命令打开命令会话并登录 Azure。
3. 输入 `azure config mode asm` 命令，在服务管理模式下运行 CLI 命令。
4. 输入以下命令创建虚拟网络（经典）：
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. 创建资源组和虚拟网络 (Resource Manager)。 可使用 CLI 1.0 或 2.0（[安装](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)）。 本教程中使用 CLI 2.0 创建虚拟网络 (Resource Manager)，因为必须使用 2.0 来创建对等互连。 从安装有 CLI 2.0.4 或更高版本的本地计算机执行以下 bash CLI 脚本。 有关在 Windows 客户端上运行 bash CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 还可使用 Azure Cloud Shell 运行该脚本。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 单击下面脚本中的“试用”按钮，调用一个可用于登录 Azure 帐户的 Cloud Shell。 若要执行脚本，请单击“复制”按钮，将内容粘贴到 Cloud Shell，然后按 `Enter`。

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. 在通过不同部署模型创建的两个虚拟网络之间创建虚拟网络对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<subscription id>` 替换为订阅 ID。如果不知道订阅 ID，请输入 `az account show` 命令。 输出中的 ID 值就是订阅 ID。将修改后的脚本粘贴到 CLI 会话，然后按 `Enter`。

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. 执行该脚本后，请检查虚拟网络 (Resource Manager) 的对等互连。 复制以下命令，将其粘贴到 CLI 会话，然后按 `Enter`：

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    该输出将在 PeeringState 列中显示“已连接”。 

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
8. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
9. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>创建对等互连 - PowerShell

1. 安装最新版本的 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 和 [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `Add-AzureAccount` 命令登录 Azure。
4. 若要通过 PowerShell 创建虚拟网络（经典），必须新建网络配置文件，或修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 该文件应包括本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素：

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 导入更改的网络配置文件会导致订阅中现有虚拟网络（经典）发生变化。 请确保只添加之前的虚拟网络，且不会从订阅中更改或删除任何现有虚拟网络。 
5. 输入 `login-azurermaccount` 命令，登录 Azure，创建虚拟网络 (Resource Manager)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
6. 创建资源组和虚拟网络 (Resource Manager)。 复制该脚本，将其粘贴到 PowerShell，然后按 `Enter`。

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. 在通过不同部署模型创建的两个虚拟网络之间创建虚拟网络对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<subscription id>` 替换为订阅 ID。如果不知道订阅 ID，请输入 `Get-AzureRmSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID。 若要执行该脚本，请从文本编辑器中复制修改后的脚本，然后在 PowerShell 会话中右键单击，然后按 `Enter`。

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. 执行该脚本后，请检查虚拟网络 (Resource Manager) 的对等互连。 复制以下命令，将其粘贴到 PowerShell 会话，然后按 `Enter`：

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    该输出将在 PeeringState 列中显示“已连接”。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

9. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
10. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。
 
## <a name="permissions"></a>权限

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 例如，如果对等互连两个名为 myVnet1 和 myVnet2 的虚拟网络，则对于每个虚拟网络，必须为帐户分配以下最低角色或权限：
    
|虚拟网络|部署模型|角色|权限|
|---|---|---|---|
|myVnet1|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|不适用|
|myVnet2|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限 Resource Manager）。

## <a name="delete"></a>删除资源
完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。

### <a name="delete-cli"></a>Azure CLI

1. 通过 Azure CLI 2.0 借助以下命令删除虚拟网络 (Resource Manager)：

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. 通过 Azure CLI 1.0 借助以下命令删除虚拟网络（经典）：

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. 输入以下命令，删除虚拟网络 (Resource Manager)：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. 若要通过 PowerShell 删除虚拟网络（经典），必须修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 删除本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素：

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 导入更改的网络配置文件会导致订阅中现有虚拟网络（经典）发生变化。 请确保只删除之前的虚拟网络，且不会从订阅中更改或删除任何其他现有虚拟网络。 

## <a name="register"></a>注册全局虚拟网络对等互连（预览版）

在同一区域中的虚拟网络之间建立对等互连的功能已推出正式版。 在不同区域的虚拟网络之间建立对等互连目前处于预览版状态。 有关可用区域，请参阅[虚拟网络更新](https://azure.microsoft.com/en-us/updates/?product=virtual-network)。 若要跨区域在虚拟网络之间建立对等互连，必须先通过使用 Azure PowerShell 或 Azure CLI 完成以下步骤（在要对其建立对等互连的每个虚拟网络所在的订阅中执行）来注册预览版：

### <a name="powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 使用 `Login-AzureRmAccount` 命令启动 PowerShell 会话并登录到 Azure。
3. 通过输入以下命令，注册要对其建立对等互连的每个虚拟网络所在订阅的预览版：

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. 输入以下命令，确认已针对预览版进行了注册：

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    输入之前的命令，在收到的两个订阅的“RegistrationState”输出为“已注册”后，才能完成本文中在门户、Azure CLI、PowerShell 或资源管理器模板部分中进行的步骤。

### <a name="azure-cli"></a>Azure CLI

1. [安装并配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)。
2. 输入 `az --version` 命令，确保使用的是版本 2.0.18 或更高版本的 Azure CLI。 如果不是，请安装最新版本。
3. 使用 `az login` 命令登录到 Azure。
4. 输入以下命令，针对预览版进行注册：

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. 输入以下命令，确认已针对预览版进行了注册：

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    输入之前的命令，在收到的两个订阅的“RegistrationState”输出为“已注册”后，才能完成本文中在门户、Azure CLI、PowerShell 或资源管理器模板部分中进行的步骤。

## <a name="next-steps"></a>后续步骤

- 在为生产用途创建虚拟网络对等互连之前，请充分熟悉重要的[虚拟网络对等互连约束和行为](virtual-network-manage-peering.md#requirements-and-constraints)。
- 了解所有的[虚拟网络对等互连设置](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何使用虚拟网络对等互连[创建中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
