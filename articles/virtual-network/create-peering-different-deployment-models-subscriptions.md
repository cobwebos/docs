---
title: "创建 Azure 虚拟网络对等互连 - 不同部署模型 - 不同订阅 | Microsoft Docs"
description: "了解如何在通过不同 Azure 订阅中的不同 Azure 部署模型创建的虚拟网络间创建虚拟网络对等互连。"
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 901bacd450561ee5eb4811320626d6ecbcc8c916
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>创建虚拟网络对等互连 - 不同部署模型和不同订阅

本文介绍如何在通过不同部署模型创建的虚拟网络间创建虚拟网络对等互连。 虚拟网络位于不同订阅。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 单击下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[均为 Resource Manager 模型](virtual-network-create-peering.md) |相同|
|[均为 Resource Manager 模型](create-peering-different-subscriptions.md) |不同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models.md) |相同|

不能在通过经典部署模型部署的两个虚拟网络之间创建对等互连。 本教程使用同一区域中的虚拟网络。 在不同区域的虚拟网络之间建立对等互连的功能现在处于预览状态。 若要使用该功能，必须[注册](#register)。 

在位于不同订阅的虚拟网络间创建虚拟网络对等互连时，两个订阅均必须与同一 Azure Active Directory 租户相关联。 如果还没有 Azure Active Directory 租户，可快速[创建一个](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)。 可以使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)连接不同订阅和不同 Azure Active Directory 租户中的虚拟网络。

可使用 [Azure 门户](#portal)、Azure [命令行接口](#cli) (CLI) 或 Azure [PowerShell](#powershell) 创建虚拟网络对等互连。 单击以前的任何工具链接直接转到使用所选工具创建虚拟网络对等互连的步骤。

## <a name="portal"></a>创建对等互连 - Azure 门户

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，跳过注销门户的步骤，及为虚拟网络分配其他用户权限的步骤。

1. 以用户 A 的身份登录 [Azure 门户](https://portal.azure.com)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
2. 依次单击“+ 新建”、“网络”、“虚拟网络”。
3. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：
    - 名称：myVnetA
    - **地址空间**：*10.0.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.0.0.0/24*
    - 订阅：选择订阅 A。
    - 资源组：选择“新建”并输入 myResourceGroupA
    - **位置**：美国东部
4. 在门户顶部的“搜索资源”框中键入 myVnetA。 单击出现在搜索结果中的“myVnetA”。 随即显示“myVnetA”虚拟网络的边栏选项卡。
5. 在显示的“myVnetA”边栏选项卡中，单击左侧垂直选项列表中的“访问控制(IAM)”。
6. 在显示的“myVnetA - 访问控制(IAM)”边栏选项卡中，单击“+ 添加”。
7. 在显示的“添加权限”边栏选项卡上的“角色”框中，选择“网络参与者”。
8. 在“选择”框中，选择用户 B，或者键入用户 B 的电子邮件地址来搜索用户名。 显示的用户列表来自要为其设置对等互连的虚拟网络所在的同一个 Azure Active Directory 租户。 单击出现在列表中的用户 B。
9. 单击“ **保存**”。
10. 以用户 A 的身份注销门户，然后以用户 B 的身份登录。
11. 单击“+ 新建”，在“搜索 Marketplace”框中键入“虚拟网络”，然后在搜索结果中单击“虚拟网络”。
12. 在显示的“虚拟网络”边栏选项卡中，在“选择部署模型”框中选择“经典”，然后单击“创建”。
13.   在显示的“创建虚拟网络(经典)”边栏选项卡中，输入以下值：

    - 名称：myVnetB
    - **地址空间**：*10.1.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.1.0.0/24*
    - 订阅：选择订阅 B。
    - 资源组：选择“新建”并输入 myResourceGroupB
    - **位置**：美国东部

14. 在门户顶部的“搜索资源”框中键入 myVnetB。 单击出现在搜索结果中的“myVnetB”。 随即显示“myVnetB”虚拟网络的边栏选项卡。
15. 在显示的“myVnetB”边栏选项卡中，单击左侧垂直选项列表中的“属性”。 复制“资源 ID”，在稍后的步骤中使用。 资源 ID 类似于以下示例：/subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. 完成 myVnetB 的步骤 5-9，输入步骤 8 中的用户 A。
17. 以用户 B 的身份注销门户，然后以用户 A 的身份登录。
18. 在门户顶部的“搜索资源”框中键入 myVnetA。 单击出现在搜索结果中的“myVnetA”。 随即显示“myVnet”虚拟网络的边栏选项卡。
19. 单击“myVnetA”。
20. 在显示的“myVnetA”边栏选项卡中，单击左侧垂直选项列表中的“对等互连”。
21. 在显示的“myVnetA - 对等互连”边栏选项卡中，单击“+ 添加”
22. 在显示的“添加对等互连”边栏选项卡中，输入或选择以下选项，然后单击“确定”：
     - 名称：myVnetAToMyVnetB
     - **虚拟网络部署模型**：选择“经典”。
     - **我知道我的资源 ID**：选中此框。
     - 资源 ID：输入步骤 15 中 myVnetB 的资源 ID。
     - **允许虚拟网络访问：**确保选择“已启用”。
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
23. 在上一步骤中单击“确定”后，“添加对等互连”边栏选项卡将会关闭，并再次显示“myVnetA - 对等互连”边栏选项卡。 几秒钟后，创建的对等互连将显示在该边栏选项卡中。 所创建的 myVnetAToMyVnetB 对等互连的“对等互连状态”列中列出了“已连接”。 现已建立对等互连。 无需将虚拟网络（经典）与虚拟网络 (Resource Manager) 进行对等。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

24. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
25. **可选：**若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>创建对等互连 - Azure CLI

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。 

1. [安装](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 1.0，创建虚拟网络（经典）。
2. 使用 `azure login` 命令打开 CLI 并以用户 B 的身份登录到 Azure。
3. 输入 `azure config mode asm` 命令，在服务管理模式下运行 CLI 命令。
4. 输入以下命令创建虚拟网络（经典）：
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. 必须通过[安装的](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 2.0.4 或更高版本使用 bash shell 或使用 Azure Cloud Shell 来完成其余步骤。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 单击下面脚本中的“试用”按钮，此时会打开一个可登录到 Azure 帐户的 Cloud Shell。 有关在 Windows 客户端上运行 CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 
6. 将以下脚本复制到计算机上的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 ID。 如果不知道订阅 ID，请输入 `az account show` 命令。 输出中的 ID 值就是订阅 ID。复制修改后的脚本，将其粘贴到 CLI 2.0 会话中，然后按 `Enter`。 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    在步骤 4 中创建虚拟网络（经典）后，Azure 在 Default-Networking 资源组中创建了虚拟网络。
7. 以用户 B 的身份注销 Azure，并以用户 A 的身份在 CLI 2.0 中登录。
8. 创建资源组和虚拟网络 (Resource Manager)。 复制以下脚本，将其粘贴到 CLI 会话中，然后按 `Enter`。 

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

9. 在通过不同部署模型创建的两个虚拟网络之间创建虚拟网络对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<SubscriptionB-id>` 替换为订阅 ID。如果不知道订阅 ID，请输入 `az account show` 命令。 输出中的 ID 值就是订阅 ID。Azure 创建了虚拟网络（经典），该网络是在步骤 4 中在名为 Default-Networking 的资源组中创建的虚拟网络。 将修改后的脚本粘贴到 CLI 会话中，然后按 `Enter`。

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. 执行该脚本后，请检查虚拟网络 (Resource Manager) 的对等互连。 复制以下脚本，将其粘贴到 CLI 会话中：

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    该输出将在 PeeringState 列中显示“已连接”。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

11. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
12. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>创建对等互连 - PowerShell

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。 

1. 安装最新版本的 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 和 [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `Add-AzureAccount` 命令，作为用户 B 登录用户 B 的订阅。
4. 若要通过 PowerShell 创建虚拟网络（经典），必须新建网络配置文件，或修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 该文件应包括本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素：

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. 输入 `login-azurermaccount` 命令，以用户 B 的身份登录用户 B 的订阅，使用 Resource Manager。
6. 向用户 A 分配访问虚拟网络 B 的权限。将以下脚本复制到电脑上的文本编辑器，并将 `<SubscriptionB-id>` 替换为订阅 B 的 ID。如果不知道订阅 ID，请输入 `Get-AzureRmSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID。 Azure 创建了虚拟网络（经典），该网络是在步骤 4 中在名为 Default-Networking 的资源组中创建的虚拟网络。 若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. 以用户 B 的身份注销 Azure，然后输入 `login-azurermaccount` 命令，以用户 A 的身份登录用户 A 的订阅。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
8. 复制以下脚本，将其粘贴到 PowerShell，然后按 `Enter`，创建虚拟网络 (Resource Manager)：

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. 向用户 B 分配访问 myVnetA 的权限。 将以下脚本复制到电脑上的文本编辑器，并将 `<SubscriptionA-Id>` 替换为订阅 A 的 ID。如果不知道订阅 ID，请输入 `Get-AzureRmSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID。 将修改后的脚本粘贴到 PowerShell，然后按 `Enter` 执行该脚本。

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. 将以下脚本复制到电脑上的文本编辑器，并将 `<SubscriptionB-id>` 替换为订阅 B 的 ID。将myVnetA 和 myVNetB 进行对等，复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. 复制以下脚本，将其粘贴到 PowerShell，按 `Enter`，查看 myVnetA 的对等互连状态。

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    状态为“已连接”。 设置从 myVnetB 到 myVnetA 的对等互连后，状态即会变为“已连接”。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

12. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
13. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="permissions"></a>权限

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 例如，如果对等互连两个名为 myVnetA 和 myVnetB 的虚拟网络，则对于每个虚拟网络，帐户必须分配有以下最低角色或权限：
    
|虚拟网络|部署模型|角色|权限|
|---|---|---|---|
|myVnetA|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|不适用|
|myVnetB|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限 Resource Manager）。

## <a name="delete"></a>删除资源
完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 myResourceGroupA。 在搜索结果中，单击“myResourceGroupA”。
2. 在“myResourceGroupA”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 myResourceGroupA，然后单击“删除”。
4. 在门户顶部的“搜索资源”框中键入 myVnetB。 单击出现在搜索结果中的“myVnetB”。 随即显示“myVnetB”虚拟网络的边栏选项卡。
5. 在“myVnetB”边栏选项卡中，单击“删除”。
6. 若要确认删除，请单击“删除虚拟网络”框中的“是”。

### <a name="delete-cli"></a>Azure CLI

1. 使用 CLI 2.0 登录 Azure，通过以下命令删除虚拟网络 (Resource Manager)：

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. 使用 Azure CLI 1.0 登录 Azure，通过以下命令删除虚拟网络（经典）：

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. 在 PowerShell 命令提示符处，输入以下命令，删除虚拟网络 (Resource Manager)：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. 若要通过 PowerShell 删除虚拟网络（经典），必须修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 删除本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素：

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

## <a name="next-steps"></a>后续步骤

- 在为生产用途创建虚拟网络对等互连之前，请充分熟悉重要的[虚拟网络对等互连约束和行为](virtual-network-manage-peering.md#requirements-and-constraints)。
- 了解所有的[虚拟网络对等互连设置](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何使用虚拟网络对等互连[创建中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
