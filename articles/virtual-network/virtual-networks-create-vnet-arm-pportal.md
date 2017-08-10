---
title: "创建包含多个子网的 Azure 虚拟网络 | Microsoft Docs"
description: "了解如何在 Azure 中创建包含多个子网的虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a31f0524a6fa1de45498f340a27b863a3c627e04
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

本教程介绍如何创建包含独立公共和私有子网的基本 Azure 虚拟网络。 可在子网中创建虚拟机、应用服务环境、虚拟机规模集、Azure HDInsight 和云服务等 Azure 资源。 虚拟网络中的资源可以彼此通信，并可以与连接到虚拟网络的其他网络中的资源通信。

以下部分提供了使用 [Azure 门户](#portal)、Azure 命令行接口 ([Azure CLI](#azure-cli))、[Azure PowerShell](#powershell) 和 [Azure 资源管理器模板](#resource-manager-template)创建虚拟网络的步骤。 不管使用哪种工具来创建虚拟网络，结果都是一样的。 单击工具链接，转到教程中该工具的对应部分。 详细了解所有[虚拟网络](virtual-network-manage-network.md)和[子网](virtual-network-manage-subnet.md)设置。

本文提供通过资源管理器部署模型（创建新虚拟网络时建议使用的部署模型）创建虚拟网络的步骤。 如果需要创建虚拟网络（经典），请参阅[创建虚拟网络（经典）](create-virtual-network-classic.md)。 如果不熟悉 Azure 的部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="portal"></a>Azure 门户

1. 在 Internet 浏览器中，转到 [Azure 门户](https://portal.azure.com)。 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户中，单击“+ 新建” > “网络” > “虚拟网络”。
3. 在“创建虚拟网络”边栏选项卡中，输入以下值，然后单击“创建”：

    |设置|值|
    |---|---|
    |Name|myVnet|
    |地址空间|10.0.0.0/16|
    |子网名称|公共|
    |子网地址范围|10.0.0.0/24|
    |资源组|保留选中“新建”，输入 **myResourceGroup**。|
    |订阅和位置|选择订阅和位置。

    如果不熟悉 Azure，请详细了解[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions)（也称为“区域”）。
4. 在创建虚拟网络时，只能在门户中创建一个子网。 在本教程中，将在创建虚拟网络之后创建第二个子网。 随后可在“公共”子网中创建可通过 Internet 访问的资源。 还可以在“专用”子网中创建无法通过 Internet 访问的资源。 若要创建第二个子网，请在页面顶部的“搜索资源”框中输入 **myVnet**。 在搜索结果中，单击“myVnet”。 如果在订阅中存在多个同名的虚拟网络，请检查每个虚拟网络下列出的资源组。 确保单击的是包含“myResourceGroup”资源组的“myVnet”搜索结果。
5. 在“myVnet”边栏选项卡中，单击“设置”下面的“子网”。
6. 在“myVnet - 子网”边栏选项卡中单击“+子网”。
7. 在“添加子网”边栏选项卡中，在“名称”处输入“私有”。 在“地址范围”处输入“10.0.1.0/24”。  单击 **“确定”**。
8. 在“myVnet - 子网”边栏选项卡中查看子网。 可以看到已创建的“公共”和“私有”子网。
9. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)中所述步骤。

## <a name="azure-cli"></a>Azure CLI

无论是从 Windows、 Linux 还是 macOS 执行命令，Azure CLI 命令都相同。 不过在操作系统 shell 之间存在脚本差异。 以下步骤中的脚本在 Bash shell 中执行。 

1. [安装并配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。 无需安装 CLI 及其必备组件，而可以使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 Cloud Shell 预安装有 Azure CLI 并将其配置为与帐户一起使用。 若要使用 Cloud Shell，请单击[门户](https://portal.azure.com)顶部的“Cloud Shell”(**>_**) 按钮，或者在后面的步骤中单击“试用”按钮。 
2. 如果在本地运行 CLI，请使用 `az login` 命令登录到 Azure。 如果使用的是 Cloud Shell，则已经登录。
3. 查看以下脚本及其注释。 在浏览器中，复制该脚本并将其粘贴到 CLI 会话中：

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. 运行完脚本后，请查看虚拟网络的子网。 复制以下命令，并将其粘贴到 CLI 会话中：

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在 PowerShell 会话中，使用 `login-azurermaccount` 命令以 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。

3. 查看以下脚本及其注释。 在浏览器中，复制该脚本并将其粘贴到 PowerShell 会话中：

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. 若要查看虚拟网络的子网，请复制以下命令，并将其粘贴到 PowerShell 会话中：

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="resource-manager-template"></a>资源管理器模板

可使用 Azure 资源管理器模板部署虚拟网络。 若要详细了解模板，请参阅[什么是资源管理器](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)。 若要访问模板并了解其参数，请参阅[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板。 可以使用[门户](#template-portal)、[Azure CLI](#template-cli) 或 [PowerShell](#template-powershell) 部署模板。

可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete)任何子节中所述的步骤。

### <a name="template-portal"></a>Azure 门户

1. 在浏览器中打开[模板页](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)。
2. 单击“**部署到 Azure**”按钮。 如果尚未登录到 Azure，请在显示的 Azure 门户登录屏幕中登录。
3. 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到门户。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 输入以下参数值：

    |参数|值|
    |---|---|
    |订阅|选择订阅|
    |资源组|myResourceGroup|
    |位置|选择一个位置|
    |VNet 名称|myVnet|
    |VNet 地址前缀|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公共|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. 同意条款和条件，然后单击“购买”以部署虚拟网络。

### <a name="template-cli"></a>Azure CLI

1. [安装并配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保已安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。 无需安装 CLI 及其必备组件，而可以使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 Cloud Shell 预安装有 Azure CLI 并将其配置为与帐户一起使用。 若要使用 Cloud Shell，请单击[门户](https://portal.azure.com)顶部的“Cloud Shell”**>_** 按钮，或者在后面的步骤中单击“试用”按钮。 
2. 如果在本地运行 CLI，请使用 `az login` 命令登录到 Azure。 如果使用的是 Cloud Shell，则已经登录。
3. 若要为虚拟网络创建资源组，请复制以下命令，并将其粘贴到 CLI 会话中：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. 可以使用下列参数选项之一来部署模板：
    - 默认参数值。 输入以下命令：
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - 自定义参数值。 在部署模板之前，下载并修改模板。 还可以通过在命令行使用参数来部署模板，或使用单独的参数文件部署模板。 若要下载模板和参数文件，请在[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板页上单击“在 GitHub 上浏览”按钮。 在 GitHub 中单击“azuredeploy.parameters.json”或“azuredeploy.json”文件。 然后，单击“Raw”按钮以显示该文件。 在浏览器中，复制文件的内容。 将内容保存至计算机上的文件。 可以修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在 PowerShell 会话中，若要使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录，请输入 `login-azurermaccount`。
3. 若要为虚拟网络创建资源组，请输入以下命令：

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. 可以使用下列参数选项之一来部署模板：
    - 默认参数值。 输入以下命令：
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - 自定义参数值。 在部署模板之前，下载并修改模板。 还可以通过在命令行使用参数来部署模板，或使用单独的参数文件部署模板。 若要下载模板和参数文件，请在[创建包含两个子网的虚拟网络](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)模板页上单击“在 GitHub 上浏览”按钮。 在 GitHub 中单击“azuredeploy.parameters.json”或“azuredeploy.json”文件。 然后，单击“Raw”按钮以显示该文件。 在浏览器中，复制文件的内容。 将内容保存至计算机上的文件。 可以修改模板中的参数值，或使用单独的参数文件部署模板。  

    若要详细了解如何使用这些方法部署模板，请键入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>删除资源

完成本教程后，可以删除创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。

### <a name="delete-cli"></a>Azure CLI

在 CLI 会话中输入以下命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 会话中输入以下命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 若要了解有关所有虚拟网络和子网设置的信息，请参阅[管理虚拟网络](virtual-network-manage-network.md#view-vnet)和[管理虚拟网络子网](virtual-network-manage-subnet.md#create-subnet)。 在生产环境中有使用虚拟网络和子网的多种选项，以满足不同的要求。
- 若要筛选入站和出站子网流量，请创建[网络安全组](virtual-networks-nsg.md)并将其应用到子网。
- 创建 [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机，并将其连接到现有的虚拟网络。
- 若要在同一 Azure 位置连接两个虚拟网络，请在虚拟网络之间创建[虚拟网络对等互连](virtual-network-peering-overview.md)。
- 使用 [VPN 网关](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 线路将虚拟网络连接到本地网络。

