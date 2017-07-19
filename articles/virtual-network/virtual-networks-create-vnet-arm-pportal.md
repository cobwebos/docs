---
title: "创建包含子网的 Azure 虚拟网络 | Microsoft Docs"
description: "了解如何在 Azure 中创建包含多个子网的虚拟网络。"
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

本教程介绍如何创建包含独立公共和私有子网的基本 Azure 虚拟网络。 可将虚拟机、Azure 应用服务环境、虚拟机规模集、Azure HDInsight 和其他云服务等 Azure 资源连接到子网。 已连接到虚拟网络的资源可在专用 Azure 网络上互相之间进行通信。

以下部分包括各步骤，可采取这些步骤通过使用 [Azure 门户](#portal)、Azure 命令行接口 ([Azure CLI](#azure-cli))、[Azure PowerShell](#powershell) 以及 [Azure Resource Manager 模板](#resource-manager-template)来部署虚拟网络。 不管使用哪种工具来部署虚拟网络，结果都是一样的。 单击工具链接，转到教程中该工具的对应部分。 若要了解有关所有虚拟网络和子网设置的详细信息，请参阅[管理虚拟网络](virtual-network-manage-network.md)和[管理虚拟子网](virtual-network-manage-subnet.md)。

## <a name="portal"></a>Azure 门户

1. 在 Internet 浏览器中，转到 [Azure 门户](https://portal.azure.com)。 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户中，单击“+ 新建” > “网络” > “虚拟网络”。
3. 在“虚拟网络”边栏选项卡中的“选择部署模型”下，保留选择“Resource Manager”，然后单击“创建”。
4. 在“创建虚拟网络”边栏选项卡中，输入以下值，然后单击“创建”：

    |设置|值|
    |---|---|
    |名称|MyVnet|
    |地址空间|10.0.0.0/16|
    |子网名称|公共|
    |子网地址范围|10.0.0.0/24|
    |资源组|保留选中“新建”，然后输入“MyResourceGroup”。|
    |订阅和位置|选择订阅和位置。

    如果不熟悉 Azure，请详细了解[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions)（也称为“区域”）。
6. 在创建虚拟网络时，只能在门户中创建一个子网。 在本教程中，将在创建虚拟网络之后创建第二个子网。 随后可能需将 Internet 可访问的资源连接到“公共”子网。 还可能需将无法从 Internet 访问的资源连接到“私有”子网。 若要创建第二个子网，请在页面顶部的“搜索资源”框中输入“MyVnet”。 在搜索结果中，单击“MyVnet”。 如果在订阅中存在多个同名的虚拟网络，请检查每个虚拟网络下列出的资源组。 确保单击的是包含“MyResourceGroup”资源组的“MyVnet”搜索结果。
7. 在“MyVnet”边栏选项卡中，单击“设置”下面的“子网”。
8. 在“MyVnet - 子网”边栏选项卡中单击“+ 子网”。
9. 在“添加子网”边栏选项卡中，在“名称”处输入“私有”。 在“地址范围”处输入“10.0.1.0/24”。  单击**“确定”**。
10. 在“MyVnet - 子网”边栏选项卡中查看子网。 可以看到已创建的“公共”和“私有”子网。
11. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)中所述步骤。

## <a name="azure-cli"></a>Azure CLI

无论是从 Windows、 Linux 还是 macOS 执行命令，Azure CLI 命令都相同。 不过在操作系统 shell 之间存在脚本差异。 以下说明适用于执行包含 Azure CLI 命令的 Bash 脚本：

1. 在 Internet 浏览器中，打开 [Azure 门户](https://portal.azure.com)。 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户页顶部的“搜索资源”框右侧，单击“>_”图标启动 Bash Azure Cloud Shell（预览版）。 Cloud Shell 窗格将显示在门户底部。 几秒钟后会出现 username@Azure:~ $ 提示。 Cloud Shell 自动使用登录门户所用的凭据将你登录到 Azure。
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
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. 创建脚本文件并保存。 在 Cloud Shell 命令提示符处键入 `nano myscript.sh --nonewlines`。 该命令使用空的 myscript.sh 文件启动 GNU nano 编辑器。 将光标置于编辑器窗口内，右键单击，然后单击“粘贴”。  
5. 若要将文件另存为 myscript.sh，请按 Ctrl+X，键入“Y”，然后按 Enter 键。 切换不同的会话时，Cloud Shell 存储不会保留已保存的文件。 若要在切换 Cloud Shell 会话时保留脚本，请为 Cloud Shell 设置[持久存储](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，并把文件保存到“持久存储”中。
6. 在 Cloud Shell 命令提示符下，运行 `chmod +x myscript.sh` 命令将该文件标记为可执行文件。
7. 若要执行脚本，输入 `./myscript.sh`。
8. 脚本运行完成后，若要查看虚拟网络的子网，请复制以下命令，然后将其粘贴在 Bash Cloud Shell 窗格中：
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要启动 PowerShell 会话，请转到“开始”，输入“powershell”，然后单击“PowerShell”。
3. 在 PowerShell 窗口中，若要使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录，请输入 `login-azurermaccount`。
4. 在浏览器中复制以下脚本：
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. 若要执行该脚本，请在 PowerShell 窗口中单击右键。
6. 若要查看虚拟网络的子网，请复制以下命令，然后将其粘贴在 PowerShell 窗口中：
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="resource-manager-template"></a>Resource Manager 模板

可使用 Azure Resource Manager 模板部署虚拟网络。 若要详细了解模板，请参阅[什么是 Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)。 若要访问模板并了解其参数，请参阅[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板。 可以使用[门户](#template-portal)、[Azure CLI](#template-cli) 或 [PowerShell](#template-powershell) 部署模板。

可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete)任何子节中所述的步骤。

### <a name="template-portal"></a>Azure 门户

1. 在浏览器中打开[模板页](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)。
2. 单击“**部署到 Azure**”按钮。 这将打开 Azure 门户登录页。
3. 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到门户。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 输入以下参数值：

    |参数|值|
    |---|---|
    |订阅|选择订阅|
    |资源组|MyResourceGroup|
    |位置|选择一个位置|
    |VNet 名称|MyVnet|
    |VNet 地址前缀|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公共|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. 同意条款和条件，然后单击“购买”以部署虚拟网络。

### <a name="template-cli"></a>Azure CLI

1. 在[门户](https://portal.azure.com)中，使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在“搜索资源”框右侧，单击“>_”图标启动 Bash Azure Cloud Shell（预览版）。 Cloud Shell 窗格将显示在门户底部。 几秒钟后会出现 username@Azure:~ $ 提示。 Cloud Shell 自动使用登录 Azure 门户所用的凭据将你登录到 Azure。
3. 若要为虚拟网络创建资源组，请输入以下命令： `az group create --name MyResourceGroup --location eastus`
4. 可以使用下列参数选项之一来部署模板：
    - 默认参数值。 输入以下命令：  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - 自定义参数值。 在部署模板之前，下载并修改模板。 还可以通过在命令行使用参数来部署模板，或使用单独的参数文件部署模板。 若要下载模板和参数文件，请在[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板页上单击“在 GitHub 上浏览”按钮。 在 GitHub 中单击“azuredeploy.parameters.json”或“azuredeploy.json”文件。 然后，单击“Raw”按钮以显示该文件。 在浏览器中，复制文件的内容。 将内容保存至计算机上的文件。 可以修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要启动 PowerShell 会话，请转到“开始”，输入“powershell”，然后单击“PowerShell”。
3. 在 PowerShell 窗口中，若要使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录，请输入 `login-azurermaccount`。
4. 若要为虚拟网络创建资源组，请输入以下命令： `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. 可以使用下列参数选项之一来部署模板：
    - 默认参数值。 输入以下命令：  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - 自定义参数值。 在部署模板之前，下载并修改模板。 还可以通过在命令行使用参数来部署模板，或使用单独的参数文件部署模板。 若要下载模板和参数文件，请在[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板页上单击“在 GitHub 上浏览”按钮。 在 GitHub 中单击“azuredeploy.parameters.json”或“azuredeploy.json”文件。 然后，单击“Raw”按钮以显示该文件。 在浏览器中，复制文件的内容。 将内容保存至计算机上的文件。 可以修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>删除资源
完成本教程之后，可能会想要删除本教程中用到的资源，以避免产生使用费。 删除资源组还将删除该资源组中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入“MyResourceGroup”。 在搜索结果中，单击“MyResourceGroup”。
2. 在“MyResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入“MyResourceGroup”，然后单击“删除”。

### <a name="delete-cli"></a>Azure CLI

在 Cloud Shell 命令提示符下输入以下命令：`az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 命令提示符下输入以下命令：`Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>后续步骤

- 若要了解有关所有虚拟网络和子网设置的信息，请参阅[管理虚拟网络](virtual-network-manage-network.md#view-vnet)和[管理虚拟子网](virtual-network-manage-subnet.md#create-subnet)。 在生产环境中有使用虚拟网络和子网的多种选项，以满足不同的要求。
- 通过创建[网络安全组](virtual-networks-nsg.md) (NSG) 并将其应用到子网来筛选入站和出站子网流量。
- 创建 [Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，然后将其连接到虚拟网络。
- 通过使用[虚拟网络对等互连](virtual-network-peering-overview.md)将虚拟网络连接到同一位置中的另一个虚拟网络。
- 使用[站点到站点虚拟专用网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 线路，将虚拟网络连接到本地网络。

