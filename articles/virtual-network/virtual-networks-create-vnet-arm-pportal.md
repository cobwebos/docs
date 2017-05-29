---
title: "创建 Azure 虚拟网络 | Microsoft Docs"
description: "了解如何创建包含多个子网的虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

本教程介绍如何创建包含独立公共和专用子网的基本 Azure 虚拟网络 (VNet)。 可将虚拟机 (VM)、应用服务环境、虚拟机规模集、HDInsight 和云服务等 Azure 资源连接到子网。 连接到 Vnet 的资源可通过专用 Azure 网络相互通信。

以下部分包含有关使用 Azure [门户](#portal)、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell) 和 Azure Resource Manager [模板](#template)部署 VNet 的步骤。 不管选择哪种工具来部署 VNet，结果都是一样的。 单击任一工具的链接会直接转到本文的相关部分。 若要详细了解所有的 VNet 和子网设置，请阅读文章[管理 VNet](virtual-network-manage-network.md) 和[管理子网](virtual-network-manage-subnet.md)。

## <a name="portal"></a>Azure 门户

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户中，单击“+新建” > “网络” > “虚拟网络”。
3. 在显示的“虚拟网络”边栏选项卡中，保留“选择部署模型”下面选中的“Resource Manager”，然后单击“创建”。
4. 在显示的“创建虚拟网络”边栏选项卡中输入以下值，然后单击“创建”按钮：

    |设置|值|
    |---|---|
    |名称|*MyVnet*|
    |地址空间|*10.0.0.0/16*|
    |子网名称|公共|
    |子网地址范围|*10.0.0.0/24*|
    |资源组|保留选中“新建”并输入 *MyResourceGroup*|
    |订阅和位置|选择订阅和位置。

    如果你不熟悉 Azure，请详细了解[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions)（也称为区域）。
6. 在门户中创建 VNet 时，只能创建一个子网。 对于本教程，将在创建 VNet 后创建第二个子网。 以后，可以将可通过 Internet 访问的资源连接到*公共*子网，将无法通过 Internet 访问的资源连接到专用子网。 若要创建第二个子网，请在门户顶部的“搜索资源”框中输入 *MyVnet*。 当“MyVnet”出现在搜索结果中时，请单击它。 如果订阅中有多个同名的 VNet，每个同名的 VNet 下面会列出资源组名称。 请确保单击其下面包含 *MyResourceGroup* 的 MyVnet 结果。
7. 在显示的“MyVnet”边栏选项卡中，单击“设置”下面的“子网”。
8. 在“MyVnet - 子网”边栏选项卡中，单击“+子网”。
9. 在“添加子网”边栏选项卡中为“名称”输入“专用”，为“地址范围”输入“10.0.1.0/24”，然后单击“确定”。
10. 在“MyVnet - 子网”边栏选项卡中检查子网。 将会看到所创建的“公共”和“专用”子网。
11. **可选：**若要删除在本教程中创建的资源，请完成本文[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>CLI
无论是在 Windows、Linux 还是 macOS 上，执行 CLI 命令的结果都是相同的，但是，脚本在不同操作系统 shell 中的结果会有差异。 以下说明适用于执行包含 CLI 命令的 Bash 脚本：

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户顶部“搜索资源”栏的右侧，单击“>_”图标启动 Bash Azure Cloud Shell（预览）。 Cloud Shell 窗格显示在门户底部，几秒钟后将显示 **username@Azure:~$** 提示符。 Cloud Shell 使用在门户中进行身份验证时所用的凭据将你登录到 Azure。
3. 在浏览器中复制以下脚本：
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. 创建脚本文件并保存。 在 Cloud Shell 提示符下，键入 `nano myscript.sh --nonewlines` 该命令使用空的 myscript.sh 文件启动 GNU nano 编辑器。 在编辑器窗口中单击右键，然后单击“粘贴”。 切换不同的会话后，Cloud Shell 存储不会保留。 若要在切换 Cloud Shell 会话后保留脚本，请为 Cloud Shell 设置[持久存储](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 
5. 在键盘上按住 **Ctrl+X** 键的同时输入 **Y**，然后按 **Enter** 键将该文件保存为 myscript.sh。
6. 在 Cloud Shell 提示符下，使用 `chmod +x myscript.sh` 命令将该文件标记为可执行文件。
7. 输入 `./myscript.sh` 执行该脚本。
8. 脚本完成后，可以通过在 Bash Cloud Shell 中复制并粘贴以下命令来查看 VNet 的子网：
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **可选：**若要删除在本教程中创建的资源，请完成本文[删除资源](#delete-cli)部分中所述的步骤。

## <a name="powershell"></a>PowerShell
1. 安装最新版本的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果你不太熟悉 Azure PowerShell，请阅读 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 单击 Windows“开始”按钮，键入 **powershell**，然后在搜索结果中单击“PowerShell”，启动 PowerShell 会话。
3. 在 PowerShell 窗口中输入 `login-azurermaccount` 命令，使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 在浏览器中复制以下脚本：
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. 若要执行该脚本，请在 PowerShell 窗口中单击右键。
6. 通过在 PowerShell 窗口中复制并粘贴以下命令来查看 VNet 的子网：
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **可选：**若要删除在本教程中创建的资源，请完成本文[删除资源](#delete-powershell)部分中所述的步骤。

## <a name="template"></a>模板

可以使用 Azure Resource Manager 模板部署 VNet。 若要详细了解模板，请参阅[什么是 Resource Manager ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)一文。 若要访问模板并了解其参数，请查看[使用两个子网模板创建 VNet](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 网页。 可以使用[门户](#template-portal)、[CLI](#template-cli) 或 [PowerShell](#template-powershell) 部署模板。

**可选：**若要删除在本教程中创建的资源，请完成本文[删除资源](#delete)部分的所有小节中所述的步骤。

### <a name="template-portal"></a>门户

1. 在浏览器中打开[网页](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)模板。
2. 单击“部署到 Azure”按钮，此时会打开 Azure 门户登录页。
3. 使用 Azure 帐户登录到[门户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 输入以下参数值：

    |参数|值|
    |---|---|
    |订阅|选择你的订阅。|
    |资源组|MyResourceGroup|
    |位置|选择一个位置。|
    |VNet 名称|MyVnet|
    |VNet 地址前缀|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公共|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. 同意条款和条件，然后单击“购买”部署 VNet。

### <a name="template-cli"></a>CLI

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户顶部“搜索资源”栏的右侧，单击“>_”图标启动 Bash Azure Cloud Shell（预览）。 Cloud Shell 窗格显示在门户底部，几秒钟后将显示 **username@Azure:~$** 提示符。 Cloud Shell 使用在门户中进行身份验证时所用的凭据将你登录到 Azure。
3. 输入以下命令为 VNet 创建资源组： `az group create --name MyResourceGroup --location eastus`
4. 可以使用以下值部署模板：
    - **默认参数值：**输入以下命令：  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **自定义参数值：**部署模板之前下载并修改模板、使用命令行中的参数部署模板，或使用单独的参数文件部署模板。 可以在[使用两个子网模板创建 VNet](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 网页上单击“在 GitHub 上浏览”按钮，来下载模板和参数文件。 在 GitHub 中单击 **azuredeploy.parameters.json** 或**azuredeploy.json** 文件，然后单击该文件对应的“Raw”按钮。 在浏览器中，复制内容并将其保存到计算机上的某个文件。 修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安装最新版本的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果你不太熟悉 Azure PowerShell，请阅读 [Azure PowerShell 概述](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 单击 Windows“开始”按钮，键入 **powershell**，然后在搜索结果中单击“PowerShell”，启动 PowerShell 会话。
3. 在 PowerShell 窗口中输入 `login-azurermaccount` 命令，使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 输入以下命令为 VNet 创建资源组： `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. 可以使用以下值部署模板：
    - **默认参数值：**为此，请输入以下命令：  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **自定义参数值：**为此，可以部署模板之前下载并修改模板、使用命令行中的参数部署模板，或使用单独的参数文件部署模板。 可以在[使用两个子网模板创建 VNet](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 网页上单击“在 GitHub 上浏览”按钮，来下载模板和参数文件。 在 GitHub 中单击 **azuredeploy.parameters.json** 或**azuredeploy.json** 文件，然后单击该文件对应的“Raw”按钮。 在浏览器中，复制内容并将其保存到计算机上的某个文件。 修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>删除资源
完成本教程后，可以删除资源，以免产生使用费。 删除某个资源组也会删除其中包含的所有资源。

### <a name="delete-portal"></a>门户

1. 在门户顶部的“搜索资源”框中开始键入 *MyResourceGroup*。 当“MyResourceGroup”出现在搜索结果中时，请单击它。
2. 在显示的“MyResourceGroup”边栏选项卡中，单击顶部附近的“删除”图标。
3. 若要确认删除，请在“键入资源组名称:”框中输入 *MyResourceGroup*，然后单击“删除”。

### <a name="delete-cli"></a>CLI

在 Cloud Shell 提示符下输入以下命令：`az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 提示符下输入以下命令：`Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>后续步骤

- 若要了解所有的 VNet 和子网设置，请阅读文章[管理 VNet](virtual-network-manage-network.md#view-vnet) 和[管理子网](virtual-network-manage-subnet.md#create-subnet)。 可以根据不同的要求，使用多种选项创建生产 VNet 和子网。
- 通过创建[网络安全组](virtual-networks-nsg.md) (NSG) 并将其应用到子网来筛选入站和出站子网流量。
- 创建 [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 并将其连接到 VNet。
- 使用 [VNet 对等互连](virtual-network-peering-overview.md)将 VNet 连接到同一位置中的其他 VNet。
- 使用[站点到站点虚拟专用网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (VPN) 或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 线路将 VNet 连接到本地网络。

