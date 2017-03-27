---
title: "Azure 虚拟机的多个 IP 地址 - PowerShell | Microsoft Docs"
description: "了解如何使用 PowerShell | Resource Manager 为虚拟机分配多个 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: acf5ae8dc98213fe435f8feafe4a8ef246f545b9
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>使用 PowerShell 将多个 IP 地址分配到虚拟机

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文介绍如何使用 PowerShell 通过 Azure Resource Manager 部署模型创建虚拟机 (VM)。 无法将多个 IP 地址分配到通过经典部署模型创建的资源。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>创建具有多个 IP 地址的 VM

下面的步骤说明如何根据方案中所述，创建具有多个 IP 地址的示例 VM。 在自己的实施项目中，需要更改变量名称和 IP 地址类型。

1. 打开 PowerShell 命令提示符，在单个 PowerShell 会话中完成本部分余下的步骤。 如果尚未安装并配置 PowerShell，请先完成[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中所述的步骤。
2. 完成[创建 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤 1-4。 不要完成步骤 5（创建公共 IP 资源和网络接口）。 如果更改了此文中使用的任何变量的名称，请同样更改剩余步骤中的变量的名称。 若要创建 Linux VM，请选择 Linux 操作系统而不是 Windows。
3. 键入以下命令，创建一个变量用于存储“创建 Windows VM”一文的步骤 4（创建 VNet）中创建的子网对象：

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
4. 定义要分配给 NIC 的 IP 配置。 可以根据需要添加、删除或更改配置。 本方案说明了以下配置：

    **IPConfig-1**

    输入以下命令来创建：
    - 具有静态公共 IP 地址的公共 IP 地址资源
    - 具有公共 IP 地址资源和动态专用 IP 地址的 IP 配置

    ```powershell
    $myPublicIp1    = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    请注意上述命令中的 `-Primary` 开关。 为 NIC 分配多个 IP 配置时，必须将一个配置指定为 *Primary*。

    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。

    **IPConfig-2**

    将以下 **$IPAddress** 变量的值更改为所创建的子网中某个可用且有效的地址。 若要检查地址 10.0.0.5 是否在子网上可用，请输入命令 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`。 如果该地址可用，输出将返回 *True*。 如果该地址不可用，输出将返回 *False* 以及可用的地址列表。 输入以下命令，创建一个新的公共 IP 地址资源，以及一个具有静态公共 IP 地址和静态专用 IP 地址的新 IP 配置：
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = "10.0.0.5"
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    输入以下命令，创建具有动态专用 IP 地址且没有公共 IP 地址的 IP 配置：

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
5. 输入以下命令，使用上一步骤中定义的 IP 配置创建 NIC。

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > 尽管本文是将所有 IP 配置分配到单个 NIC，但你也可以将多个 IP 配置分配到 VM 中的任意 NIC。 若要了解如何创建具有多个 NIC 的 VM，请阅读[创建具有多个 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md)一文。

6. 完成[创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤 6。 

    > [!WARNING]
    > 如果存在以下情况，“创建 VM”一文中的步骤 6 将会失败：
    > - 在此文的步骤 6 中将名为 $myNIC 的变量更改成了其他值。
    > - 尚未完成本文的前述步骤，并且未完成“创建 VM”一文中的步骤。
    >
7. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源：

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. 将专用 IP 地址添加到 VM 操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分针对操作系统的步骤即可。 请勿向操作系统添加公共 IP 地址。

## <a name="add"></a>将 IP 地址添加到 VM

完成以下步骤即可将专用和公共 IP 地址添加到 NIC。 以下部分中的示例假定用户的 VM 已完成本文[方案](#Scenario)中描述的三项 IP 配置，但这不是必需的。

1. 打开 PowerShell 命令提示符，在单个 PowerShell 会话中完成本部分余下的步骤。 如果尚未安装并配置 PowerShell，请先完成[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中所述的步骤。
2. 将以下 $Variable 的“值”分别更改为要向其添加 IP 地址的 NIC 名称，以及 NIC 所在的资源组和位置：

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    如果不知道要更改的 NIC 名称，请输入以下命令，然后更改上述变量的值：

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. 键入以下命令创建变量，并将它设置为现有的 NIC：

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
4. 在以下命令中，请将 *myVNet* 和 *mySubnet* 更改为 NIC 连接到的 VNet 和 子网的名称。 输入以下命令，检索 NIC 连接到的 VNet 和子网对象：

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    如果不知道 NIC 所连接到的 VNet 或子网名称，请输入以下命令：
    ```powershell
    $mynic.IpConfigurations
    ```
    在返回的输出中查看类似于下面的文本：
    ```powershell
    Subnet   : {
                 "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    ```
    在此输出中， *myVnet* 是 NIC 连接到的 VNet，*mySubnet* 是连接到的子网。

5. 根据要求完成以下部分之一中的步骤：

    **添加专用 IP 地址**

    若要将专用 IP 地址添加到 NIC，必须创建 IP 配置。 以下命令创建具有静态 IP 地址 10.0.0.7 的配置。 如果想要添加动态专用 IP 地址，请在输入命令之前删除 `-PrivateIpAddress 10.0.0.7`。 指定静态 IP 地址时，该地址必须是未使用的子网地址。 建议先通过输入 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` 命令来测试该地址，确保它可用。 如果该 IP 地址可用，输出将返回 *True*。 如果该地址不可用，输出将返回 *False* 以及可用的地址列表。

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    使用唯一的配置名称和专用 IP 地址创建任意数目的配置（适用于具有静态 IP 地址的配置）。

    将专用 IP 地址添加到 VM 操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分针对操作系统的步骤即可。

    **添加公共 IP 地址**

    将公共 IP 地址资源关联到新 IP 配置或现有 IP 配置即可添加公共 IP 地址。 根据需要完成以下部分之一中的步骤。

    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。
    >

    - **将公共 IP 地址资源关联到新 IP 配置**
    
        每当在新 IP 配置中添加公共 IP 地址时，还必须添加一个专用 IP 地址，因为所有 IP 配置都必须有专用 IP 地址。 可以添加现有的公共 IP 地址资源，或新建一个。 若要新建此类资源，请输入以下命令：
    
        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

         若要新建具有动态专用 IP 地址和关联的 *myPublicIp3* 公共 IP 地址资源的 IP 配置，请输入以下命令：

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
         $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

    - **将公共 IP 地址资源关联到现有 IP 配置**

        公共 IP 地址资源只能关联到尚未与任何公共 IP 地址资源关联的 IP 配置。 输入以下命令即可确定某个 IP 配置是否具有关联的公共 IP 地址：

        ```powershell
        $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        将显示类似于下面的输出：<br>

            Name       PrivateIpAddress PublicIpAddress                                           Primary
            
            IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
            IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
            IpConfig-3 10.0.0.6                                                                     False

        *IpConfig-3* 的 **PublicIpAddress** 列为空，这表示该 IP 配置当前没有任何关联的公共 IP 地址资源。 可将现有公共 IP 地址资源添加到 IpConfig-3，或输入以下命令来创建一个：

        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

        输入以下命令，将公共 IP 地址资源关联到名为 *IpConfig-3* 的现有 IP 配置：
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

6. 输入以下命令，使用新 IP 配置设置 NIC：

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

7. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源：

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. 将专用 IP 地址添加到 VM 操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分针对操作系统的步骤即可。 请勿向操作系统添加公共 IP 地址。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

