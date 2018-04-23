---
title: 创建包含多个子网的 Azure 虚拟网络（经典）| Microsoft Docs
description: 了解如何在 Azure 中创建包含多个子网的虚拟网络（经典）。
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: c41a1057bb87c70362477d221b69ca3f5137dec4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>创建包含多个子网的虚拟网络（经典）

> [!IMPORTANT]
> Azure 有两个用于创建和处理资源的[不同部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：资源管理器和经典模型。 本文介绍使用经典部署模型。 Microsoft 建议通过[资源管理器](quick-create-portal.md)部署模型新建大多数虚拟网络。

本教程介绍如何创建包含独立公共子网和专用子网的基本 Azure 虚拟网络（经典）。 可以在子网中创建虚拟机和云服务等 Azure 资源。 在虚拟网络（经典）中创建的资源可以彼此通信，并可以与连接到虚拟网络的其他网络中的资源通信。

详细了解所有[虚拟网络](manage-virtual-network.md)和[子网](virtual-network-manage-subnet.md)设置。

> [!WARNING]
> [禁用订阅](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit)后，Azure 会立即删除虚拟网络（经典）。 不管虚拟网络（经典）中是否存在资源，都会删除虚拟网络。 如果以后重新启用订阅，必须重新创建虚拟网络中存在的资源。

可以使用 [Azure 门户](#portal)、[Azure 命令行接口 (CLI) 1.0](#azure-cli) 或 [PowerShell](#powershell) 创建虚拟网络（经典）。

## <a name="portal"></a>门户

1. 在 Internet 浏览器中，转到 [Azure 门户](https://portal.azure.com)。 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户中，单击“创建资源”。
3. 在显示的“新建”窗格顶部的“搜索 Marketplace”中输入“虚拟网络”。 单击搜索结果中出现的“虚拟网络”。
4. 在显示的“虚拟网络”窗格的“选择部署模型”框中选择“经典”，然后单击“创建”。 
5. 在“创建虚拟网络(经典)”窗格中输入以下值，然后单击“创建”：

    |设置|值|
    |---|---|
    |名称|myVnet|
    |地址空间|10.0.0.0/16|
    |子网名称|公共|
    |子网地址范围|10.0.0.0/24|
    |资源组|保留选中“新建”，输入 **myResourceGroup**。|
    |订阅和位置|选择订阅和位置。

    如果不熟悉 Azure，请详细了解[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions)（也称为“区域”）。
4. 在创建虚拟网络时，只能在门户中创建一个子网。 在本教程中，将在创建虚拟网络之后创建第二个子网。 随后可在“公共”子网中创建可通过 Internet 访问的资源。 还可以在“专用”子网中创建无法通过 Internet 访问的资源。 若要创建第二个子网，请在页面顶部的“搜索资源”框中输入 **myVnet**。 单击显示在搜索结果中的“myVnet”。
5. 在显示的“创建虚拟网络(经典)”窗格中单击“子网”（在“设置”部分中）。
6. 在显示的“myVnet - 子网”窗格中单击“+添加”。
7. 在“添加子网”窗格中，为“名称”输入“专用”。 为“地址范围”输入 **10.0.1.0/24**。  单击“确定”。
8. 在“myVnet - 子网”窗格中，可以看到已创建的“公共”和“专用”子网。
9. **可选**：完成本教程后，可以删除创建的资源，以免产生使用费：
    - 在“myVnet”窗格中单击“概述”。
    - 在“myVnet”窗格中单击“删除”图标。
    - 若要确认删除，请单击“删除虚拟网络”框中的“是”。

## <a name="azure-cli"></a>Azure CLI

1. 可以[安装并配置 Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，或者在 Azure Cloud Shell 中使用 CLI。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 若要获取 CLI 命令的帮助，请键入 `azure <command> --help`。 
2. 在 CLI 会话中，使用以下命令登录到 Azure。 如果在下框中单击“试用”，则会打开 Cloud Shell。 无法输入以下命令即可登录到 Azure 订阅：

    ```azurecli-interactive
    azure login
    ```

3. 为确保 CLI 处于服务管理模式，请输入以下命令：

    ```azurecli-interactive
    azure config mode asm
    ```

4. 创建包含专用子网的虚拟网络：

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. 在虚拟网络中创建公共子网：

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. 查看虚拟网络和子网：

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **可选**：完成本教程后，可以删除创建的资源，以免产生使用费：

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> 尽管无法使用 CLI 指定要在其中创建虚拟网络（经典）的资源组，但 Azure 会在名为 *Default-Networking* 的资源组中创建虚拟网络。

## <a name="powershell"></a>PowerShell

1. 安装最新版本的 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `Add-AzureAccount` 命令登录到 Azure。
4. 相应地更改以下路径和文件名，并导出现有的网络配置文件：

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. 若要创建包含公共子网和专用子网的虚拟网络，请使用任何文本编辑器将以下 **VirtualNetworkSite** 元素添加到网络配置文件。

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    查看完整的[网络配置文件架构](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

6. 导入网络配置文件：

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > 导入更改的网络配置文件会导致订阅中现有虚拟网络（经典）发生变化。 请确保只添加之前的虚拟网络，且不会从订阅中更改或删除任何现有虚拟网络。 

7. 查看虚拟网络和子网：

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **可选**：完成本教程后，可以删除创建的资源，以免产生使用费。 若要删除虚拟网络，请再次完成步骤 4-6，但这一次请删除步骤 5 中添加的 **VirtualNetworkSite** 元素。
 
> [!NOTE]
> 尽管无法使用 PowerShell 指定要在其中创建虚拟网络（经典）的资源组，但 Azure 会在名为 *Default-Networking* 的资源组中创建虚拟网络。

---

## <a name="next-steps"></a>后续步骤

- 若要了解有关所有虚拟网络和子网设置的信息，请参阅[管理虚拟网络](manage-virtual-network.md)和[管理虚拟网络子网](virtual-network-manage-subnet.md)。 在生产环境中有使用虚拟网络和子网的多种选项，以满足不同的要求。
- 若要筛选入站和出站子网流量，请创建[网络安全组](virtual-networks-nsg.md)并将其应用到子网。
- 创建 [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机，并将其连接到现有的虚拟网络。
- 若要在同一 Azure 位置连接两个虚拟网络，请在虚拟网络之间创建[虚拟网络对等互连](create-peering-different-deployment-models.md)。 可将虚拟网络（资源管理器）对等互连到虚拟网络（经典），但不能在两个虚拟网络（经典）之间创建对等互连。
- 使用 [VPN 网关](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 线路将虚拟网络连接到本地网络。
