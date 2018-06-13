---
title: 创建具有静态公共 IP 地址的 VM - Azure PowerShell | Microsoft 文档
description: 了解如何使用 PowerShell 创建具有静态公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525126"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>使用 PowerShell 创建具有静态公共 IP 地址的 VM

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell（经典）](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../resource-manager-deployment-model.md)。 本文介绍如何使用 Resource Manager 部署模型。Microsoft 建议对大多数新的部署使用该模型，而不是经典部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>启动脚本
可以在[此处](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1)下载所用的完整 PowerShell 脚本。 请按以下步骤更改要在环境中使用的脚本。

根据需要用于部署的值更改以下变量的值。 以下值映射到本文中使用的方案：

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>为 VM 创建必要的资源
在创建 VM 之前，需要可供 VM 使用的资源组、VNet、公共 IP 和 NIC。

1. 创建新的资源组。

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. 创建 VNet 和子网。

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. 创建公共 IP 资源。 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. 使用公共 IP 为上面创建的子网中的 VM 创建网络接口 (NIC)。 请注意用于从 Azure 检索 VNet 的第一个 cmdlet。必须使用该 cmdlet，因为已通过执行 `Set-AzureRmVirtualNetwork` 更改了现有的 VNet。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. 创建存储帐户，以托管 VM OS 驱动器。

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>创建 VM
现在，所有必需的资源均已就绪，可以创建新的 VM 了。

1. 创建 VM 配置对象。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. 获取 VM 本地管理员帐户的凭据。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. 创建 VM 配置对象。

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. 设置 VM 的操作系统映像。

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. 配置 OS 磁盘。

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. 将 NIC 添加到 VM。

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. 创建 VM。

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. 保存脚本文件。

## <a name="run-the-script"></a>运行脚本

进行任何必要的更改后，运行前面的脚本。 几分钟后会创建虚拟机。

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。 我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-powershell.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

## <a name="next-steps"></a>后续步骤

任何网络流量都可流入和流出本文中创建的 VM。 可以在网络安全组中定义入站和出站安全规则，以限制可以流入和流出网络接口和/或子网的流量。 若要深入了解网络安全组，请参阅[网络安全组概述](security-overview.md)。