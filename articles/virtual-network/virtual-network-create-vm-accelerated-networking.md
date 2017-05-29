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
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>创建具有加速网络的虚拟机

本教程介绍如何创建具有加速网络的 Azure 虚拟机 (VM)。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个虚拟机 (VM) 之间的通信：

![比较](./media/virtual-network-create-vm-accelerated-networking/image1.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要详细了解虚拟交换机，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 在不使用加速网络的情况下由虚拟交换机应用的所有网络策略将会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 Azure 虚拟网络 (VNet) 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="Limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新 NIC 启用加速网络。 不能为现有 NIC 启用。
* **VM 创建：**已启用加速网络的 NIC 只能在创建 VM 时附加到该 VM。 该 NIC 无法附加到现有 VM。
* **区域：**具有加速网络的 Windows VM 已在大多数 Azure 区域中提供。 只在两个区域中提供了具有加速网络的 Linux VM：美国中南部和美国西部 2 区。 将来会增加支持此功能的区域。
* **支持的操作系统：**Windows：Microsoft Windows Server 2012 R2 Datacenter 和 Windows Server 2016。 Linux：包含 4.4.0-77 或更高版本内核的 Ubuntu Server 16.04 LTS。 即将添加其他发行版。
* **VM 大小：**具有八个或多个核心的通用和计算优化实例大小。 有关详细信息，请参阅有关 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 支持的 VM 实例大小组合今后会不断增加。

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
6. 单击“DS4_V2 标准”，然后单击“选择”按钮。
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
8. 在 MyVm VM 上，单击 Windows“开始”按钮，然后单击“Internet Explorer”。
9. 在显示的“Internet Explorer 11”框中，单击“使用推荐的安全性、隐私和兼容性设置”，然后单击“确定”。
10. 在 Internet Explorer 地址栏中输入 https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84，然后 Enter 键。
11. 在显示的“安全警报”框中单击“确定”。
12. 在显示的“Internet Explorer”框中，依次单击“添加”、“受信任的站点”框中的“添加”按钮、“关闭”按钮。 针对显示的所有后续框完成这些步骤。
13. 若要下载文件，请单击它。
14. 显示“许可和使用条款”框时，请单击“我同意”。
15. 在屏幕底部显示的框中单击“保存”按钮允许 Internet Explorer 保存文件，然后单击“打开文件夹”按钮。
16. 若要安装加速网络驱动程序，请双击该文件。 在运行安装向导期间，请接受所有默认值，然后在运行完向导时单击“是”按钮重新启动 VM。
17. VM 重新启动后，请再次完成步骤 9-12 以连接到 VM。
18. 右键单击 Windows“开始”按钮，然后单击“设备管理器”。 展开“网络适配器”节点。 确认已显示“Mellanox ConnectX-3 Virtual Function Ethernet Adapter”，如下图所示：
   
    ![设备管理器](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>创建 Linux VM
可以使用 Azure 门户或 Azure [PowerShell](#linux-powershell) 创建 VM。

### <a name="linux-portal"></a>门户
1. 完成本文[创建 Linux VM -  PowerShell](#linux-powershell)部分中所述的步骤 1-5，注册适用于 Linux 预览版的加速网络。  无法在门户中为预览版注册。
2. 完成本文[创建 Windows VM - 门户](#windows-portal)部分中所述的步骤 1-8。 在步骤 2 中，请单击“Ubuntu Server 16.04 LTS”，而不要单击“Windows Server 2016 Datacenter”。 对于本教程，请选择使用密码而不是 SSH 密钥，但对于生产部署，可以使用两者之一。 如果完成本文[创建 Windows VM - 门户](#windows-portal)部分中所述的步骤 7 后“加速网络”未显示，则原因可能是下列其中一项：
    - 尚未为预览版注册。 根据本文[创建 Linux VM - Powershell](#linux-powershell) 部分中步骤 4 所述，确认注册状态为“已注册”。 **注意：**如果你已参与 Windows VM 预览版的加速网络（不再需要注册即可使用适用于 Windows VM 的加速网络），则尚未自动注册适用于 Linux VM 预览版的加速网络。 必须注册适用于 Linux VM 预览版的加速网络才能参与它。
    - 未选择本文[限制](#simitations)部分中列出的 VM 大小、操作系统或位置。
3. 若要安装适用于 Linux 的加速网络驱动程序，请完成本文[配置 Linux](#configure-linux) 部分中所述的步骤。

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>如果在订阅中创建具有加速网络的 Linux VM，然后尝试在同一订阅中创建具有加速网络的 Windows VM，创建 Windows VM 可能会失败。 在此预览版推出期间，我们建议在不同的订阅中测试具有加速网络的 Linux 和 Windows VM。
>

1. 安装最新版本的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果你不太熟悉 Azure PowerShell，请阅读 [Azure PowerShell 概述](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 单击 Windows“开始”按钮，键入 **powershell**，然后在搜索结果中单击“PowerShell”，启动 PowerShell 会话。
3. 在 PowerShell 窗口中输入 `login-azurermaccount` 命令，使用 Azure [帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。 如果还没有帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 完成以下步骤，注册适用于 Azure 预览版的加速网络：
    - 输入以下命令：

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - 将包含 Azure 订阅 ID 和目标用途的电子邮件发送到 [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)。 
    - 输入以下命令确认已针对预览版注册：
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        输入上述命令后，只有输出中显示了“已注册”时，才继续执行步骤 5。 在继续之前，输出必须类似于以下输出：
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >如果你已参与 Windows VM 预览版的加速网络（不再需要注册即可使用适用于 Windows VM 的加速网络），则尚未自动注册适用于 Linux VM 预览版的加速网络。 必须注册适用于 Linux VM 预览版的加速网络才能参与它。
      >
5. 在浏览器中复制以下脚本：
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
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
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
5. Azure 会打开一个框，告知需要输入 `ssh adminuser@<ipaddress>`。 在 Cloud Shell 中输入此命令（或者从步骤 4 中显示的框复制此命令，将它粘贴到 Cloud Shell，然后按 Enter）。
6. 出现是否继续连接的问题时，请输入“是”，然后按 Enter。
7. 输入创建 VM 时所输入的密码。 成功登录到 VM 后，将会看到 adminuser@MyVm:~ $ 提示符。 现已通过 Cloud Shell 会话登录到 VM。 **注意：**在处于非活动状态 10 分钟后，Cloud Shell 会话将会超时。
8. 在提示符下输入 `uname -r`，并确认输出是否与以下版本匹配：“4.4.0-77-generic”。
9.    运行以下命令，在标准网络 vNIC 与加速网络 vNIC 之间创建绑定。 网络流量使用较高性能的加速网络 vNIC，而绑定可确保网络流量不会在发生某些配置更改时中断。 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults`注意：如果有错误指出“insserv: 警告: 脚本 bondvf.sh 缺少 LSB 标记和重写”，可以忽略该错误。
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` 打开 GNU nano 编辑器来编辑文件。
    - 在编辑器中，在 *auto etho0* 和 *iface eth0 inet dhcp* 行的开头添加 *#*，注释掉这些行。 在每行中添加 *#* 后，这些行如以下示例所示：
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. 在按住 **Ctrl+X** 键的同时输入 **Y**，然后按 **Enter** 键保存该文件。
11. 输入 `sudo shutdown -r now` 命令重新启动 VM。
12. 重新启动 VM 后，再次完成步骤 5-7 与它重新连接。
13.    运行 `ifconfig` 命令并确认 bond0 是否已启动，以及接口是否显示为 UP。 **注意：**使用加速网络的应用程序必须通过 *bond0* 接口而不是 *eth0* 通信。  在加速网络推出正式版之前，接口名称可以会更改。

