---
title: "创建 Azure 虚拟网络对等互连 - Resource Manager - 不同订阅 | Microsoft Docs"
description: "了解如何在通过不同 Azure 订阅中的 Resource Manager 创建的虚拟网络间创建虚拟网络对等互连。"
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
ms.openlocfilehash: 51ca159945098c2467124db07f68caf06794b9e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>创建虚拟网络对等互连 - Resource Manager，不同订阅 

本教程介绍如何在通过 Resource Manager 创建的虚拟网络间创建虚拟网络对等互连。 虚拟网络位于不同订阅。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。 

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 单击下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[均为 Resource Manager 模型](tutorial-connect-virtual-networks-portal.md) |相同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models.md) |相同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models-subscriptions.md) |不同|

不能在通过经典部署模型部署的两个虚拟网络之间创建对等互连。 如需连接两个通过经典部署模型创建的虚拟网络，可使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来连接它们。 

本教程将在同一区域中的虚拟网络之间建立对等互连。 在不同区域的虚拟网络之间建立对等互连的功能目前处于预览版状态。 首先完成[注册全局虚拟网络对等互连](#register)中的步骤，然后再尝试在不同区域中的虚拟网络之间建立对等互连，否则对等互连的建立将失败。 使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)在不同区域连接虚拟网络的功能现已公开发布且无需注册。

可以使用 [Azure 门户](#portal)、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell)、或 [Azure 资源管理器模板](#template)创建虚拟网络对等互连。 单击以前的任何工具链接直接转到使用所选工具创建虚拟网络对等互连的步骤。

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
8. 在“选择”框中，选择用户 B，或者键入用户 B 的电子邮件地址来搜索用户。 显示的用户列表来自要为其设置对等互连的虚拟网络所在的同一个 Azure Active Directory 租户。
9. 单击“ **保存**”。
10. 在“myVnetA - 访问控制 (IAM)”边栏选项卡中，单击左侧垂直选项列表中的“属性”。 复制“资源 ID”，在稍后的步骤中使用。 资源 ID 类似于以下示例：/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA。
11. 以用户 A 的身份注销门户，然后以用户 B 的身份登录。
12. 完成步骤 2-3，在步骤 3 中输入或选择以下值：

    - 名称：myVnetB
    - **地址空间**：*10.1.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.1.0.0/24*
    - 订阅：选择订阅 B。
    - 资源组：选择“新建”并输入 myResourceGroupB
    - **位置**：美国东部

13. 在门户顶部的“搜索资源”框中键入 myVnetB。 单击出现在搜索结果中的“myVnetB”。 随即显示“myVnetB”虚拟网络的边栏选项卡。
14. 在显示的“myVnetB”边栏选项卡中，单击左侧垂直选项列表中的“属性”。 复制“资源 ID”，在稍后的步骤中使用。 资源 ID 类似于以下示例：/subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB。
15. 单击“myVnetB”边栏选项卡中的“访问控制 (IAM)”，然后完成 myVnetB 的步骤 5-10，在步骤 8 中输入 UserA。
16. 以用户 B 的身份注销门户，然后以用户 A 的身份登录。
17. 在门户顶部的“搜索资源”框中键入 myVnetA。 单击出现在搜索结果中的“myVnetA”。 随即显示“myVnet”虚拟网络的边栏选项卡。
18. 单击“myVnetA”。
19. 在显示的“myVnetA”边栏选项卡中，单击左侧垂直选项列表中的“对等互连”。
20. 在显示的“myVnetA - 对等互连”边栏选项卡中，单击“+ 添加”
21. 在显示的“添加对等互连”边栏选项卡中，输入或选择以下选项，然后单击“确定”：
     - 名称：myVnetAToMyVnetB
     - **虚拟网络部署模型**：选择“Resource Manager”。
     - **我知道我的资源 ID**：选中此框。
     - 资源 ID：输入步骤 14 中的资源 ID。
     - **允许虚拟网络访问：**确保选择“已启用”。
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
22. 在上一步骤中单击“确定”后，“添加对等互连”边栏选项卡将会关闭，并再次显示“myVnetA - 对等互连”边栏选项卡。 几秒钟后，创建的对等互连将显示在该边栏选项卡中。 创建的 myVnetAToMyVnetB 对等互连的“对等互连状态”列中列出了“已启动”。 已将 myVnetA 对等互连到 myVnetB，但现在必须将 myVnetB 对等互连到 myVnetA。 必须朝两个方向创建对等互连才能让虚拟网络中的资源相互通信。
23. 注销用户 A 的门户登录，然后以用户 B 的身份登录。
24. 针对 myVnetB 再次完成步骤 17-21。 在步骤 21 中，命名对等互连 myVnetBToMyVnetA，为虚拟网络选择 myVnetA，并在“资源ID”框中输入步骤 10 中的 ID。
25. 单击“确定”创建 myVnetB 的对等互连几秒钟后，将会列出刚刚创建的 myVnetBToMyVnetA 对等互连，“对等互连状态”列中显示“已连接”。
26. 以用户 B 的身份注销门户，然后以用户 A 的身份登录。
27. 再次完成步骤 17-19。 myVnetAToVNetB 对等互连的“对等互连状态”现也显示为“已连接”。 对等互连中两个虚拟网络的“对等互连状态”列都显示为“已连接”后，即表示已成功建立对等互连。 在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
28. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
29. **可选：**若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>创建对等互连 - Azure CLI

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。

以下脚本：

- 需要 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 在 Bash shell 中操作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

除安装 CLI 及其依赖项外，还可使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 单击下面脚本中的“试用”按钮，调用一个可用于登录 Azure 帐户的 Cloud Shell。 

1. 使用 `azure login` 命令打开 CLI 会话并以用户 A 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
2. 将以下脚本复制到电脑上的文本编辑器，将 `<SubscriptionA-Id>` 替换为订阅 A 的 ID，然后复制修改后的脚本，将其粘贴到 CLI 会话，按 `Enter`。 如果不知道订阅 ID，请输入“az account show”命令。 输出中的 ID 值就是订阅 ID。

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. 使用 `az logout` 命令注销用户 A 的 Azure 登录，然后以用户 B 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
4. 创建 myVnetB。 将步骤 2 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 B 的 ID。 将 10.0.0.0/16 更改为 10.1.0.0/16，将所有 A 更改为 B，并将所有 B 更改为 A。复制修改后的脚本，将其粘贴到 CLI 会话，然后按 `Enter`。 
5. 注销用户 B 的 Azure 登录，然后以用户 A 的身份登录 Azure。
6. 创建从 myVnetA 到 myVnetB 的虚拟网络对等互连。 将以下脚本内容复制到计算机上的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 B 的 ID。 若要执行该脚本，请复制修改后的脚本，将其粘贴到 CLI 会话，然后按 Enter。
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. 查看 myVnetA 的对等互连状态。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    状态为“已启动”。 创建从 myVnetB 到 myVnetA 的对等互连后，状态即会变为“已连接”。

8. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录 Azure。
9. 创建从 myVnetB 到 myVnetA 的对等互连。 将步骤 6 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 A 的 ID，将所有 A 更改为 B，并将所有 B 更改为 A。更改完成后，复制修改后的脚本，将其粘贴到 CLI 会话，按 `Enter`。
10. 查看 myVnetB 的对等互连状态。 将步骤 7 中的脚本内容复制到电脑的文本编辑器。 将资源组和虚拟网络名称中的 A 更改为 B，复制该脚本，将修改后的脚本粘贴到 CLI 会话，然后按 `Enter`。 对等互连状态为“已连接”。 创建从 myVnetB 到 myVnetA 的对等互连后，myVnetA 的对等互连状态变为“已连接”。 可以用户 A 的身份重新登录 Azure，并再次完成步骤 7，验证 myVnetA 的对等互连状态。 

    > [!NOTE]
    > 直到两个虚拟网络的对等互连状态均为“已连接”时，对等互连才建立成功。

11. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
12. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
 
## <a name="powershell"></a>创建对等互连 - PowerShell

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `login-azurermaccount` 命令以用户 A 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
4. 创建资源组和虚拟网络 A。将以下脚本复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 A 的 ID。 如果不知道订阅 ID，请输入 `Get-AzureRmSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID。 若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
6. 将步骤 4 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 B 的 ID。将 10.0.0.0/16 更改为 10.1.0.0/16。 将所有 A 更改为 B，并将所有 B 更改为 A。若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
7. 注销用户 B 的 Azure 登录，然后以用户 A 的身份登录。
8. 创建从 myVnetA 到 myVnetB 的对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 B 的 ID。若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. 查看 myVnetA 的对等互连状态。

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    状态为“已启动”。 设置从 myVnetB 到 myVnetA 的对等互连后，状态即会变为“已连接”。

10. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
11. 创建从 myVnetB 到 myVnetA 的对等互连。 将步骤 8 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 A 的 ID，并分别将所有 A 更改为 B，将所有 B 更改为 A。要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
12. 查看 myVnetB 的对等互连状态。 将步骤 9 中的脚本内容复制到电脑的文本编辑器。 将资源组和虚拟网络名称中的 A 更改为 B。 若要执行该脚本，请将修改后的脚本粘贴到 PowerShell，然后按 `Enter`。 状态为“已连接”。 创建从 myVnetB 到 myVnetA 的对等互连后，myVnetA 的对等互连状态变为“已连接”。 可以用户 A 的身份重新登录 Azure，并再次完成步骤 9，验证 myVnetA 的对等互连状态。 

    > [!NOTE]
    > 直到两个虚拟网络的对等互连状态均为“已连接”时，对等互连才建立成功。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

13. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
14. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="template"></a>创建对等互连 - 资源管理器模板

1. 要创建虚拟网络，并将正确的[权限](#permissions)分配给每个订阅中的帐户，请完成本文[门户](#portal)、[Azure CLI](#cli) 或 [PowerShell](#powershell)部分中所述步骤。
2. 将下面的文本保存到本地计算机上的某个文件中。 将 `<subscription ID>` 替换为用户 A 的订阅 ID。 例如，可能会将文件另存为 vnetpeeringA.json。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. 以用户 A 的身份登录 Azure，并使用[门户](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)、[PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine) 或 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) 来部署模板。 指定在步骤 2 中用于保存示例 json 文本的文件的文件名。
4. 将步骤 2 中的示例 json 复制到本地计算机上的某地文件中，并对如下开头的行进行更改：
    - name：将 myVnetA/myVnetAToMyVnetB 更改为 myVnetB/myVnetBToMyVnetA。
    - id：将 `<subscription ID>` 替换为用户 B 的订阅 ID，并将 myVnetB 更改为 myVnetA。
5. 再次完成步骤 3，以用户 B 的身份登录 Azure。
6. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
7. **可选：**若要删除在本教程中创建的资源，请使用 Azure 门户、PowerShell 或 Azure CLI 完成本文的[删除资源](#delete)部分中所述的步骤。

## <a name="permissions"></a>权限

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 例如，如果对等互连两个名为 myVnetA 和 myVnetB 的虚拟网络，则对于每个虚拟网络，必须为帐户分配以下最低角色或权限：
    
|虚拟网络|角色|权限|
|---|---|---|
|myVnetA|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限资源管理器）。

## <a name="delete"></a>删除资源
完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 以用户 A 的身份登录 Azure 门户。
2. 在门户的搜索框中，输入 myResourceGroupA。 在搜索结果中，单击“myResourceGroupA”。
3. 在“myResourceGroupA”边栏选项卡中，单击“删除”图标。
4. 若要确认删除，请在“键入资源组名称”框中输入 myResourceGroupA，然后单击“删除”。
5. 注销用户 A 的门户登录，然后以用户 B 的身份登录。
6. 完成 myResourceGroupB 的步骤 2-4.

### <a name="delete-cli"></a>Azure CLI

1. 以用户 A 的身份登录 Azure，并执行以下命令：

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
3. 运行以下命令：

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. 以用户 A 的身份登录 Azure，并执行以下命令：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
3. 运行以下命令：

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

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
