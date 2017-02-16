---
title: "虚拟机的多个 IP 地址 - PowerShell | Microsoft 文档"
description: "了解如何使用 PowerShell | Resource Manager 为虚拟机分配多个 IP 地址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: a0d8a6dbe793bc4ea5211e772439d931c8e84a04
ms.openlocfilehash: cf2a57f96576b18692dd42a9680d7f3b8d8f7c69


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>使用 PowerShell 将多个 IP 地址分配到虚拟机

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

在一个 Azure 虚拟机 (VM) 上可以附加一个或多个网络接口 (NIC)。 可为任何 NIC 分配一个或多个静态或动态的公共与专用 IP 地址。 为 VM 分配多个 IP 地址可实现以下功能：

* 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
* 用作网络虚拟设备，例如防火墙或负载均衡器。
* 可将任何 NIC 的任何专用 IP 地址添加到 Azure Load Balancer 后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要详细了解如何对多个 IP 配置进行负载均衡，请阅读 [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md)（负载均衡多个 IP 配置）一文。

附加到 VM 的每个 NIC 具有一个或多个关联的 IP 配置。 每个配置分配有一个静态或动态专用 IP 地址。 每个配置还可以有一个关联的公共 IP 地址资源。 公共 IP 地址资源分配有动态或者静态 IP 地址。 如果你不熟悉 Azure 中的 IP 地址，请参阅 [Azure 中的 IP 地址](virtual-network-ip-addresses-overview-arm.md)一文了解详细信息。

本文说明如何使用 PowerShell 将多个 IP 地址分配到通过 Azure Resource Manager 部署模型创建的 VM。 无法将多个 IP 地址分配到通过经典部署模型创建的资源。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>方案
创建具有单个 NIC 的 VM 并将其连接到虚拟网络。 该 VM 需要三个不同的*专用* IP 地址和两个*公共* IP 地址。 IP 地址将分配到以下 IP 配置：

* **IPConfig-1：**分配一个*动态*专用 IP 地址（默认）和一个*静态*公共 IP 地址。
* **IPConfig-2：**分配一个*静态*专用 IP 地址和一个*静态*公共 IP 地址。
* **IPConfig-3：**分配一个*动态*专用 IP 地址，但不分配公共 IP 地址。
  
    ![多个 IP 地址](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

创建 NIC 时，IP 配置将关联到 NIC；创建 VM 时，NIC 将附加到 VM。 本方案使用的 IP 地址类型用于演示。 可以根据需要分配任何 IP 地址和指定分配类型。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>创建具有多个 IP 地址的 VM

下面的步骤说明如何根据方案中所述，创建具有多个 IP 地址的示例 VM。 在自己的实施项目中，需要更改变量名称和 IP 地址类型。

1. 打开 PowerShell 命令提示符，在单个 PowerShell 会话中完成本部分余下的步骤。 如果尚未安装并配置 PowerShell，请先完成[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 一文中所述的步骤。
2. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 如果不遵循所收到的电子邮件中的说明
3. 完成[创建 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md) 一文中的步骤 1-4。 不要完成步骤 5（创建公共 IP 资源和网络接口）。 如果更改了此文中使用的任何变量的名称，请同样更改剩余步骤中的变量的名称。 若要创建 Linux VM，请选择 Linux 操作系统而不是 Windows。
4. 键入以下命令，创建一个变量用于存储“创建 Windows VM”一文的步骤 4（创建 VNet）中创建的子网对象：

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. 定义要分配给 NIC 的 IP 配置。 可以根据需要添加、删除或更改配置。 本方案说明了以下配置：

    **IPConfig-1**

    输入以下命令来创建：
    - 具有静态公共 IP 地址的公共 IP 地址资源
    - 具有公共 IP 地址资源和动态专用 IP 地址的 IP 配置

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    请注意上述命令中的 `-Primary` 开关。 为 NIC 分配多个 IP 配置时，必须将一个配置指定为 *Primary*。

    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。
    >

    **IPConfig-2**

    将以下 **$IPAddress** 变量的值更改为所创建的子网中某个可用且有效的地址。 若要检查地址 10.0.0.5 是否在子网上可用，请输入命令 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`。 如果该地址可用，输出将返回 *True*。 如果该地址不可用，输出将返回 *False* 以及可用的地址列表。 输入以下命令，创建一个新的公共 IP 地址资源，以及一个具有静态公共 IP 地址和静态专用 IP 地址的新 IP 配置：
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
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
6. 输入以下命令，使用上一步骤中定义的 IP 配置创建 NIC。

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > 尽管本文是将所有 IP 配置分配到单个 NIC，但你也可以将多个 IP 配置分配到 VM 中的任意 NIC。 若要了解如何创建具有多个 NIC 的 VM，请阅读[创建具有多个 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md)一文。

7. 完成[创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md) 一文中的步骤 6。 

    > [!WARNING]
    > 如果存在以下情况，“创建 VM”一文中的步骤 6 将会失败：
    > - 在此文的步骤 6 中将名为 $myNIC 的变量更改成了其他值。
    > - 尚未完成本文的前述步骤，并且未完成“创建 VM”一文中的步骤。
    >
8. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源：

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>将 IP 地址添加到 VM 操作系统

连接并登录到使用多个专用 IP 地址创建的 VM。 必须手动添加 VM 中的所有专用 IP 地址（包括主要地址）。 根据 VM 操作系统完成以下步骤：

### <a name="windows"></a>Windows

1. 在命令提示符下，键入 *ipconfig /all*。  只能看到*主要*专用 IP 地址（通过 DHCP）。
2. 在命令提示符下键入 *ncpa.cpl*，打开“网络连接”窗口。
3. 打开“连接连接”的属性。
4. 双击“Internet 协议版本 4 (IPv4)”。
5. 单击“使用下面的 IP 地址”并输入以下值：

    * **IP 地址**：输入*主要*专用 IP 地址
    * **子网掩码**：根据子网设置此值。 例如，如果子网为 /24 子网，则子网掩码为 255.255.255.0。
    * **默认网关**：子网中的第一个 IP 地址。 如果子网为 10.0.0.0/24，则网关 IP 地址为 10.0.0.1。
    * 单击“使用下面的 DNS 服务器地址”并输入以下值：
        * **首选 DNS 服务器**：如果不使用自己的 DNS 服务器，请输入 168.63.129.16。  如果使用自己的 DNS 服务器，请输入服务器的 IP 地址。
    * 单击“高级”按钮，然后添加其他 IP 地址。 使用为主 IP 地址指定的相同子网，为 NIC 添加步骤 8 中列出的每个辅助专用 IP 地址。
    * 单击“确定”关闭“TCP/IP 设置”，然后再次单击“确定”关闭适配器设置。 将重新建立 RDP 连接。
6. 在命令提示符下，键入 *ipconfig /all*。 此时将显示添加的所有 IP 地址，DHCP 已关闭。
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，请输入以下命令：

    ```bash
    sudo -i
    ```

3. 更新网络接口（假设为“eth0”）的配置文件。

    * 保留 dhcp 的现有行项。 主要 IP 地址将保留以前的配置。
    * 使用以下命令添加其他静态 IP 地址的配置：

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    应会看到一个 .cfg 文件。
4. 打开该文件：vi *文件名*。

    该文件的末尾应会显示以下命令行：

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 在此文件包含的命令行后面添加以下命令行：

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. 使用以下命令保存该文件：

    ```bash
    :wq
    ```

7. 使用以下命令重置网络接口：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 如果使用远程连接，请在同一行中同时运行 ifdown 和 ifup。
    >

8. 使用以下命令验证 IP 地址是否已添加到网络接口：

    ```bash
    Ip addr list eth0
    ```

    应会在列表中看到添加的 IP 地址。
    
### <a name="linux-redhat-centos-and-others"></a>Linux（Redhat、CentOS 和其他操作系统）

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，请输入以下命令：

    ```bash
    sudo -i
    ```

3. 输入密码，根据提示的说明操作。 切换为 root 用户后，使用以下命令导航到网络脚本文件夹：

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 使用以下命令列出相关的 ifcfg 文件：

    ```bash
    ls ifcfg-*
    ```

    应会看到其中一个文件是 *ifcfg-eth0*。

5. 使用以下命令复制 *ifcfg-eth0* 文件并将它命名为 *ifcfg-eth0:0*：

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. 使用以下命令编辑 *ifcfg-eth0:0* 文件：

    ```bash
    vi ifcfg-eth1
    ```

7. 使用以下命令在文件中将设备更改为适当的名称，在本例中为 *eth0:0*：

    ```bash
    DEVICE=eth0:0
    ```

8. 更改 *IPADDR = YourPrivateIPAddress* 行以反映 IP 地址。
9. 使用以下命令保存该文件：

    ```bash
    :wq
    ```

10. 运行以下命令重新启动网络服务，确保更改成功：

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    应会在返回的列表中看到添加的 IP 地址 *eth0:0*。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>将 IP 地址添加到 VM

完成以下步骤可将其他专用和公共 IP 地址添加到现有 NIC。 这些示例是基于本文中所述的[方案](#Scenario)制作的。

1. 打开 PowerShell 命令提示符，在单个 PowerShell 会话中完成本部分余下的步骤。 如果尚未安装并配置 PowerShell，请先完成[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 一文中所述的步骤。
2. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 如果不遵循所收到的电子邮件中的说明
3. 将以下 $Variable 的“值”分别更改为要向其添加 IP 地址的 NIC 名称，以及 NIC 所在的资源组和位置：

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    如果不知道要更改的 NIC 名称，请输入以下命令，然后更改上述变量的值：

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. 键入以下命令创建变量，并将它设置为现有的 NIC：

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. 在以下命令中，请将 *myVNet* 和 *mySubnet* 更改为 NIC 连接到的 VNet 和 子网的名称。 输入以下命令，检索 NIC 连接到的 VNet 和子网对象：

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    如果不知道 NIC 所连接到的 VNet 或子网名称，请输入以下命令：
    ```powershell
    $mynic.IpConfigurations
    ```
    在返回的输出中查看类似于下面的文本：

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    在此输出中， *myVnet* 是 NIC 连接到的 VNet，*mySubnet* 是连接到的子网。

6. 根据要求完成以下部分之一中的步骤：

    **添加专用 IP 地址**
    
    若要将专用 IP 地址添加到 NIC，必须创建 IP 配置。 以下命令创建具有静态 IP 地址 10.0.0.7 的配置。 如果想要添加动态专用 IP 地址，请在输入命令之前删除 `-PrivateIpAddress 10.0.0.7`。 指定静态 IP 地址时，该地址必须是未使用的子网地址。 建议先通过输入 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` 命令来测试该地址，确保它可用。 如果该 IP 地址可用，输出将返回 *True*。 如果该地址不可用，输出将返回 *False* 以及可用的地址列表。

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    使用唯一的配置名称和专用 IP 地址创建任意数目的配置（适用于具有静态 IP 地址的配置）。

    **添加公共 IP 地址**
    
    可以通过将某个公共 IP 地址关联到新的 IP 配置或现有 IP 配置，来添加该地址。 根据需要完成以下部分之一中的步骤。

    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。
    >

    **将资源关联到新的 IP 配置**
    
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

    **将资源关联到现有 IP 配置**
   公共 IP 地址资源只能关联到尚未与任何公共 IP 地址资源关联的 IP 配置。 输入以下命令即可确定某个 IP 配置是否具有关联的公共 IP 地址：

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    在返回的输出中查看如下所示的行：

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
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

7. 输入以下命令，使用新 IP 配置设置 NIC：

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源：

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

9. 遵循本文[将 IP 地址添加到 VM 操作系统](#OsConfig)部分中的说明，将添加到 NIC 的 IP 地址添加到 VM 操作系统。



<!--HONumber=Nov16_HO3-->


