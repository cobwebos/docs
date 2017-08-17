---
title: "创建具有加速网络的 Azure 虚拟机 | Microsoft Docs"
description: "了解如何创建具有加速网络的虚拟机。"
services: virtual-network
documentationcenter: na
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
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c852a1297261504015a3a985fe14a38957d1a64a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>创建具有加速网络的虚拟机

本教程介绍如何创建具有加速网络的 Azure 虚拟机 (VM)。 加速的网络是适用于 Windows 的 GA，在公共预览版中适用于特定的 Linux 分发。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个虚拟机 (VM) 之间的通信：

![比较](./media/virtual-network-create-vm-accelerated-networking/image1.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要详细了解虚拟交换机，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 在不使用加速网络的情况下由虚拟交换机应用的所有网络策略将会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 Azure 虚拟网络 (VNet) 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

> [!WARNING]
> 此 Linux 公共预览版功能的可用性和可靠性水平可能与正式发布版不同。 此功能不受支持，可能存在功能限制，并且可能不是在所有 Azure 区域都可用。 有关此功能可用性和状态方面的最新通知，请访问 Azure 虚拟网络更新页。

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="Limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新 NIC 启用加速网络。 不能为现有 NIC 启用。
* **VM 创建：**已启用加速网络的 NIC 只能在创建 VM 时附加到该 VM。 该 NIC 无法附加到现有 VM。
* **区域：**具有加速网络的 Windows VM 已在大多数 Azure 区域中提供。 已在大多区域中提供具有加速网络的 Linux VM。 支持此功能的区域正在不断增加。 请参阅下方的 Azure 虚拟网络更新博客获取最新信息。   
* **支持的操作系统：**Windows：Microsoft Windows Server 2012 R2 Datacenter 和 Windows Server 2016。 Linux: 内核 4.4.0-77 或更高版本的 Ubuntu Server 16.04 LTS、SLES 12 SP2、RHEL 7.3 和 CentOS 7.3 （已由“Rogue Wave Software”发布）。
* **VM 大小：**具有八个或多个核心的通用和计算优化实例大小。 有关详细信息，请参阅有关 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 支持的 VM 实例大小组合今后会不断增加。
* **仅通过 Azure 资源管理器 (ARM) 进行部署：**无法使用加速网络通过 ASM/RDFE 进行部署。

当这些限制有变化时，我们会通过 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)页发出通告。

## <a name="create-a-windows-vm"></a>创建 Windows VM
可以使用 Azure 门户或 Azure [PowerShell](#windows-powershell) 创建 VM。

### <a name="windows-portal"></a>门户

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户中单击“+ 新建” > “计算” > “Windows Server 2016 Datacenter”。 
3. 在显示的“Windows Server 2016 Datacenter”边栏选项卡中，保留“选择部署模型”框中选中的“Resource Manager”，然后单击“创建”。
4. 在显示的“基本信息”边栏选项卡中输入以下值，保留剩余的默认选项，或者选择或输入自己的值，然后单击“确定”按钮：

    |设置|值|
    |---|---|
    |名称|MyVm|
    |资源组|保留选中“新建”并输入 *MyResourceGroup*|
    |位置|美国西部 2|

    如果你不熟悉 Azure，请详细了解[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions)（也称为区域）。
5. 在显示的“选择大小”边栏选项卡，在“最小核心数”框中输入 *8*，然后单击“查看所有”。
6. 单击“DS4_V2 标准”，或任何受支持的 VM 然后单击“选择”按钮。
7. 在显示的“设置”边栏选项卡中，保留所有现有设置，但要在“加速网络”下面单击“已启用”，然后单击“确定”按钮。 **注意：**如果，在上一步骤中针对本文[限制](#Limitations)部分中未列出的 VM 大小、操作系统或位置选择了值，则“加速网络”将不可见。
8. 在“摘要”边栏选项卡中，单击“确定”按钮。 Azure 随即开始创建 VM。 创建 VM 需要几分钟时间。
9. 若要安装适用于 Windows 的加速网络驱动程序，请完成本文[配置 Windows](#configure-windows) 部分中所述的步骤。

### <a name="windows-powershell"></a>PowerShell
1. 安装最新版本的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果你不太熟悉 Azure PowerShell，请阅读 [Azure PowerShell 概述](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 单击 Windows“开始”按钮，键入 **powershell**，然后在搜索结果中单击“PowerShell”，启动 PowerShell 会话。
3. 在 PowerShell 窗口中输入 `login-azurermaccount` 命令，使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 在浏览器中复制以下脚本：
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. 在 PowerShell 窗口中，通过右键单击菜单粘贴脚本，然后开始执行该脚本。 系统提示输入用户名和密码。 在下一步骤中连接到 VM 时，需要使用这些凭据登录到 VM。 如果脚本失败，并且在执行脚本之前更改了脚本中的值，请确认针对本文[限制](#Limitations)部分中未列出的 VM 大小、操作系统和位置的值。
6. 若要安装适用于 Windows 的加速网络驱动程序，请完成本文[配置 Windows](#configure-windows) 部分中所述的步骤。

### <a name="configure-windows"></a>配置 Windows
在 Azure 中创建 VM 后，必须安装适用于 Windows 的加速网络驱动程序。 完成以下步骤之前，必须使用本文中所述的[门户](#windows-portal)或 [PowerShell](#windows-powershell) 步骤创建具有加速网络的 Windows VM。 

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入 *MyVm*。 当“MyVm”出现在搜索结果中时，请单击它。
3. 在“MyVm”边栏选项卡中，单击顶部左上角的“连接”按钮。 **注意：**如果“连接”按钮下方显示“正在创建”，则表示 Azure 尚未完成创建 VM。 只有在“连接”按钮下方不再显示“正在创建”时，才能单击“连接”。
4. 允许浏览器下载 **MyVm.rdp** 文件。  下载后，单击该文件将它打开。 
5. 在显示的“远程桌面连接”框（告知无法识别远程连接的发布者）中单击“连接”按钮。
6. 在显示的“Windows 安全性”框中单击“更多选项”，然后单击“使用其他帐户”。 输入在步骤 4 中所输入的用户名和密码，然后单击“确定”按钮。
7. 在显示的“远程桌面连接”框（告知无法验证远程计算机的标识）中单击“是”按钮。
8. 右键单击 Windows“开始”按钮，然后单击“设备管理器”。 展开“网络适配器”节点。 确认已显示“Mellanox ConnectX-3 Virtual Function Ethernet Adapter”，如下图所示：
   
    ![设备管理器](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. 现在已为 VM 启用加速网络。

## <a name="create-a-linux-vm"></a>创建 Linux VM
可以使用 Azure 门户或 Azure [PowerShell](#linux-powershell) 创建 Ubuntu 或 SLES VM。 对于 RHEL 和 CentOS Vm，工作流是不同的。  请参阅以下说明。

### <a name="linux-portal"></a>门户
1. 完成本文[创建 Linux VM -  PowerShell](#linux-powershell)部分中所述的步骤 1-5，注册适用于 Linux 预览版的加速网络。  无法在门户中为预览版注册。
2. 完成本文[创建 Windows VM - 门户](#windows-portal)部分中所述的步骤 1-8。 在步骤 2 中，请单击“Ubuntu Server 16.04 LTS”，而不要单击“Windows Server 2016 Datacenter”。 对于本教程，请选择使用密码而不是 SSH 密钥，但对于生产部署，可以使用两者之一。 如果完成本文[创建 Windows VM - 门户](#windows-portal)部分中所述的步骤 7 后“加速网络”未显示，则原因可能是下列其中一项：
    - 尚未为预览版注册。 根据本文[创建 Linux VM - Powershell](#linux-powershell) 部分中步骤 4 所述，确认注册状态为“已注册”。 **注意：**如果你已参与 Windows VM 预览版的加速网络（不再需要注册即可使用适用于 Windows VM 的加速网络），则尚未自动注册适用于 Linux VM 预览版的加速网络。 必须注册适用于 Linux VM 预览版的加速网络才能参与它。
    - 未选择本文[限制](#limitations)部分中列出的 VM 大小、操作系统或位置。
3. 若要安装适用于 Linux 的加速网络驱动程序，请完成本文[配置 Linux](#configure-linux) 部分中所述的步骤。

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>如果在订阅中创建具有加速网络的 Linux VM，然后尝试在同一订阅中创建具有加速网络的 Windows VM，创建 Windows VM 可能会失败。 在此预览版推出期间，我们建议在不同的订阅中测试具有加速网络的 Linux 和 Windows VM。
>

1. 安装最新版本的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果你不太熟悉 Azure PowerShell，请阅读 [Azure PowerShell 概述](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 单击 Windows“开始”按钮，键入 **powershell**，然后在搜索结果中单击“PowerShell”，启动 PowerShell 会话。
3. 在 PowerShell 窗口中输入 `login-azurermaccount` 命令，使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 完成以下步骤，注册适用于 Azure 预览版的加速网络：
    - 将包含 Azure 订阅 ID 和目标用途的电子邮件发送到 [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)。 请等待来自 Microsoft 的有关启用订阅的邮件确认。
    - 输入以下命令确认已针对预览版注册：
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        输入上述命令后，只有输出中显示了“已注册”时，才继续执行步骤 5。 在继续之前，输出必须类似于以下输出：
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >如果你已参与 Windows VM 预览版的加速网络（不再需要注册即可使用适用于 Windows VM 的加速网络），则尚未自动注册适用于 Linux VM 预览版的加速网络。 必须注册适用于 Linux VM 预览版的加速网络才能参与它。
      >
5. 在浏览器中，根据需要复制以下脚本代替 Ubuntu 或 SLES。  同样，Redhat 和 CentOS 具有不同的工作流，如下所述：

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. 在 PowerShell 窗口中，通过右键单击菜单粘贴脚本，然后开始执行该脚本。 系统提示输入用户名和密码。 在下一步骤中连接到 VM 时，需要使用这些凭据登录到 VM。 如果脚本失败，请确认：
    - 已根据步骤 4 中所述为预览版注册
    - 如果执行脚本之前更改了脚本中的 VM 大小、操作系统类型或位置值，请确认这些值是否已在本文的[限制](#Limitations)部分中列出。
7. 若要安装适用于 Linux 的加速网络驱动程序，请完成本文[配置 Linux](#configure-linux) 部分中所述的步骤。

### <a name="configure-linux"></a>配置 Linux

在 Azure 中创建 VM 后，必须安装适用于 Linux 的加速网络驱动程序。 完成以下步骤之前，必须使用本文中所述的[门户](#linux-portal)或 [PowerShell](#linux-powershell) 步骤创建具有加速网络的 Linux VM。 

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在门户顶部“搜索资源”栏的右侧，单击“>_”图标启动 Bash Cloud Shell（预览）。 Bash Cloud Shell 窗格显示在门户底部，几秒钟后将显示 **username@Azure:~$** 提示符。 尽管可以在计算机中使用 SSH 而不是 Cloud Shell 连接到 VM，但本教程中的说明假设使用 Cloud Shell。
3. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入 *MyVm*。 当“MyVm”出现在搜索结果中时，请单击它。
4. 在“MyVm”边栏选项卡中，单击顶部左上角的“连接”按钮。 **注意：**如果“连接”按钮下方显示“正在创建”，则表示 Azure 尚未完成创建 VM。 只有在“连接”按钮下方不再显示“正在创建”时，才能单击“连接”。
5. Azure 会打开一个框，告知需要输入 `ssh adminuser@<ipaddress>`。 在 Cloud Shell 中输入此命令（或者从步骤 4 中显示的框复制此命令，将它粘贴到 Cloud Shell），然后按 Enter。
6. 出现是否继续连接的问题时，请输入“是”，然后按 Enter。
7. 输入创建 VM 时所输入的密码。 成功登录到 VM 后，将会看到 adminuser@MyVm:~ $ 提示符。 现已通过 Cloud Shell 会话登录到 VM。 **注意：**在处于非活动状态 10 分钟后，Cloud Shell 会话将会超时。

此时，说明会根据你使用的分发而有所不同。 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. 在提示符下，输入 `uname -r` 并确认版本：

    * Ubuntu 是“4.4.0-77-generic”或更高版本
    * SLES 是“4.4.59-92.20-default”或更高版本

2. 运行以下命令，在标准网络 vNIC 与加速网络 vNIC 之间创建绑定。 网络流量使用较高性能的加速网络 vNIC，而绑定可确保网络流量不会在发生某些配置更改时中断。
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. 运行脚本后，VM 将会在暂停 60 秒后重启。
4. 重新启动 VM 后，再次完成步骤 5-7 与它重新连接。
5. 运行 `ifconfig` 命令并确认 bond0 是否已启动，以及接口是否显示为 UP。 
 
 >[!NOTE]
      >使用加速网络的应用程序必须通过 bond0 接口而不是 eth0 接口通信。  在加速网络推出正式版之前，接口名称可以会更改。

#### <a name="rhelcentos"></a>RHEL/CentOS

创建 Red Hat Enterprise Linux 或 CentOS 7.3 VM 需要一些额外步骤才能加载 SR-IOV 所需的最新驱动程序和网卡虚拟函数 (VF) 驱动程序。 第一阶段的说明准备了可用于生成一个或多个预加载驱动程序的虚拟机的映像。

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>第一阶段：准备 Red Hat Enterprise Linux or CentOS 7.3 基础映像。 

1.  在 Azure 上预配 non-SRIOV CentOS 7.3 VM

2.  安装 LIS 4.2.2：
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  下载配置文件
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  取消设置此 VM

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  从 Azure 门户停止此 VM；并转到 VM 的“磁盘”，捕获 OSDisk 的 VHD URI。 该 URI 包含基础映像的 VHD 名称及其存储帐户。 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>第二阶段：在 Azure 上预配新 VM

1.  在 New- AzureRMVMConfig 上使用基础映像 VHD 捕获可在 vNIC 上启用的 AcceleratedNetworking 第一阶段来预配新 VM 基础映像：

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  VM 启动后，通过“lspci”检查 VF 设备并检查 Mellanox 输入。 例如，我们应 Ispci 输出中看到此项：
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  通过以下指令运行捆绑脚本：

    ```bash
    sudo bondvf.sh
    ```

4.  重启新的 VM:

    ```bash
    sudo reboot
    ```

VM 应配置 bond0 并启用加速网络路径。  运行 `ifconfig` 确认。

