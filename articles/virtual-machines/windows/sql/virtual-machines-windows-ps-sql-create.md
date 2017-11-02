---
title: "在 Azure PowerShell 中创建 SQL Server 虚拟机 (Resource Manager) | Microsoft Docs"
description: "提供用于创建具有 SQL Server 虚拟机库映像的 Azure VM的步骤和 PowerShell 脚本。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 33c306258b6be40f2c5cbc016e3c84e36bf61e0d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>使用 Azure PowerShell 预配 SQL Server 虚拟机 (Resource Manager)
> [!div class="op_single_selector"]
> * [门户](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>概述
本教程演示如何使用 Azure PowerShell cmdlet 创建采用 **Azure 资源管理器**部署模型的单个 Azure 虚拟机。 在本教程中，我们将从 SQL 库中的映像创建使用单个磁盘驱动器的单个虚拟机。 我们将为虚拟机要使用的存储、网络和计算资源创建新的提供程序。 如果上述任何资源存在现有的提供程序，可以改用这些提供程序。

如果需要阅读本主题的经典版本，请参阅 [Provision a SQL Server virtual machine using Azure PowerShell Classic](../classic/ps-sql-create.md)（使用 Azure PowerShell Classic 预配 SQL Server 虚拟机）。

## <a name="prerequisites"></a>先决条件
在本教程中，需要：

* 在开始之前，需要有 Azure 帐户和订阅。 如果没有订阅，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* [Azure PowerShell](/powershell/azure/overview)，最低版本 1.4.0 或以上（本教程使用 1.5.0 版编写）。
  * 若要检索版本，请键入 **Get-Module Azure -ListAvailable**。

## <a name="configure-your-subscription"></a>配置订阅
打开 Windows PowerShell，并通过运行以下 cmdlet 来建立 Azure 帐户的访问权限。 随后将出现一个用于输入凭据的登录屏幕。 使用登录 Azure 门户时所用的相同电子邮件和密码。

```PowerShell
Add-AzureRmAccount
```

成功登录后，屏幕上会显示一些信息，其中包括默认订阅的订阅名称和 ID。 除非更改为其他订阅，否则这是本教程中将要在其中创建资源的订阅。 如果有多个订阅，请运行以下 cmdlet 返回所有订阅的列表：

```PowerShell
Get-AzureRmSubscription
```
要更改为另一个订阅，请结合目标 SubscriptionName 运行以下 cmdlet。

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>定义映像变量
为了简化可用性和了解本教程中的完整脚本，我们首先要定义几个变量。 根据需要更改参数值，但在修改提供的值时，请注意与名称长度和特殊字符相关的命名限制。

### <a name="location-and-resource-group"></a>位置和资源组
使用两个变量来定义数据区域，以及会在其中为虚拟机创建其他资源的资源组。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>存储属性
使用以下变量来定义存储帐户和虚拟机所要使用的存储类型。

根据需要修改并执行以下 cmdlet 来初始化这些变量。 请注意，在本示例中，我们将使用建议用于生产工作负荷的[高级存储](../premium-storage.md)。 有关指导和其他建议的详细信息，请参阅 [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md)（Azure 虚拟机中 SQL Server 的性能最佳实践）。

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>网络属性
使用以下变量来定义网络接口、TCP/IP 分配方法、虚拟网络名称、虚拟子网名称、虚拟网络的 IP 地址范围、子网的 IP 地址范围，以及虚拟机中的网络所要使用的公共域名标签。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
```

### <a name="virtual-machine-properties"></a>虚拟机属性
使用以下变量来定义虚拟机名称、计算机名称、虚拟机大小和虚拟机的操作系统磁盘名称。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="image-properties"></a>映像属性
使用以下变量来定义要用于虚拟机的映像。 本示例使用 SQL Server 2016 Developer Edition 映像。 Developer Edition 针对测试和开发自由授权，用户只需支付运行 VM 的成本。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

请注意，可以使用 Get-AzureRmVMImageOffer 命令来获取 SQL Server 映像产品的完整列表：

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

可以使用 Get-AzureRmVMImageSku 来查看产品可用的 SKU。 以下命令显示 SQL2016SP1-WS2016 产品可用的所有 SKU。

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>创建资源组
在 Resource Manager 部署模型中，创建的第一个对象是资源组。 我们将使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet 以前面初始化的变量所定义的资源组名称和位置来创建 Azure 资源组及其资源。

执行以下 cmdlet 来创建新的资源组。

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>创建存储帐户
虚拟机需要使用存储资源来存储操作系统磁盘及 SQL Server 数据和日志文件。 为简单起见，我们将为两者创建单个磁盘。 稍后可以使用 [Add-Azure Disk](/powershell/module/azure/add-azuredisk) cmdlet 来附加其他磁盘，以便将 SQL Server 数据和日志文件放在专用磁盘上。 我们将使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet，以前面初始化的变量所定义的存储帐户名、存储 SKU 名称和位置在新资源组中创建一个标准存储帐户。

执行以下 cmdlet 来创建新的存储帐户。

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>创建网络资源
虚拟机需要使用多个网络资源来建立网络连接。

* 每个虚拟机需要一个虚拟网络。
* 必须为每个虚拟网络至少定义一个子网。
* 必须使用公共或专用 IP 地址定义网络接口。

### <a name="create-a-virtual-network-subnet-configuration"></a>创建虚拟网络子网配置
我们首先创建虚拟网络的子网配置。 在本教程中，我们将使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet 创建默认子网。 我们将使用前面初始化的变量所定义的子网名称和地址前缀来创建虚拟网络子网配置。

> [!NOTE]
> 可以使用此 cmdlet 来定义虚拟网络子网配置的其他属性，但这已超出本教程的范围。

执行以下 cmdlet 来创建虚拟子网配置。

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>创建虚拟网络
接下来，我们将使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet 创建虚拟网络。 我们将使用前面初始化的变量所定义的名称、位置和地址前缀，以及使用前一步骤中定义的子网配置，在新的资源组中创建虚拟网络。

执行以下 cmdlet 来创建虚拟网络。

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>创建公共 IP 地址
我们现已定义虚拟网络，接下来需要配置 IP 地址才能连接到虚拟机。 在本教程中，我们将使用动态 IP 地址来创建公共 IP 地址，以支持 Internet 连接。 我们将使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet，以前面初始化的变量所定义的名称、位置、分配方法和 DNS 域名标签，在前面创建的资源组中创建公共 IP 地址。

> [!NOTE]
> 可以使用此 cmdlet 来定义公共 IP 地址的其他属性，但这已超出本初步教程的范围。 也可以创建专用地址或具有静态地址的地址，但这也超出了本教程的范围。

执行以下 cmdlet 来创建公共 IP 地址。

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>创建网络接口
我们现在已准备好创建虚拟机将要使用的网络接口。 我们将使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet，以前面定义的名称、位置、子网和公共 IP 地址，在前面创建的资源组中创建网络接口。

执行以下 cmdlet 来创建网络接口。

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>配置 VM 对象
我们现已定义存储和网络资源，接下来可以定义虚拟机的计算资源。 在本教程中，我们将指定虚拟机大小和各种操作系统属性、指定我们前面创建的网络接口、定义 Blob 存储，然后指定操作系统磁盘。

### <a name="create-the-vm-object"></a>创建 VM 对象
首先指定虚拟机大小。 对于本教程，我们应指定 DS13。 我们将使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) cmdlet，以前面初始化的变量所定义的名称与大小来创建可配置的虚拟机对象。

执行以下 cmdlet 来创建虚拟机对象。

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>创建一个凭据对象，以保留本地管理员凭据的名称和密码
我们需要先提供本地管理员帐户的凭据作为安全字符串，才可以设置虚拟机的操作系统属性。 为了实现此目的，我们将使用 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet。

执行以下 cmdlet，并在 Windows PowerShell 凭据请求窗口中，键入要用于 Windows 虚拟机中本地管理员帐户的名称和密码。

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统属性
现在，我们已准备好设置虚拟机的操作系统属性。 我们使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet 将操作系统的类型设置为 Windows，要求安装[虚拟机代理](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)，指定该 cmdlet 允许使用前面初始化的变量自动更新和设置虚拟机名称、计算机名称和凭据。

执行以下 cmdlet 来设置虚拟机的操作系统属性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>将网络接口添加到虚拟机
接下来，我们将前面创建的网络接口添加到虚拟机。 我们将使用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet 和前面定义的网络接口变量来添加网络接口。

执行以下 cmdlet 来设置虚拟机的网络接口。

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>设置虚拟机所要使用的磁盘的 Blob 存储位置
接下来，我们将使用前面定义的变量，设置虚拟机所要使用的磁盘的 Blob 存储位置。

执行以下 cmdlet 来设置 Blob 存储位置。

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统磁盘属性
接下来，我们将设置虚拟机的操作系统磁盘属性。 我们将使用 [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet 指定虚拟机的操作系统来自某个映像、将缓存设置为只读（因为要在同一磁盘上安装 SQL Server），并使用前面定义的变量来定义虚拟机名称和操作系统磁盘。

执行以下 cmdlet 来设置虚拟机的操作系统磁盘属性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>指定虚拟机的平台映像
最后一个配置步骤是指定虚拟机的平台映像。 在本教程中，我们使用最新的 SQL Server 2016 CTP 映像。 我们将通过 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet 来使用前面定义的变量所定义的这个映像。

执行以下 cmdlet 来指定虚拟机的平台映像。

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>创建 SQL VM
现在已完成配置步骤，接下来可以创建虚拟机。 我们将通过 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet，使用定义的变量来创建虚拟机。

执行以下 cmdlet 来创建虚拟机。

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

虚拟机已创建。 请注意，由于为虚拟机磁盘指定的存储帐户是高级存储帐户，因此将创建标准存储帐户用于启动诊断。

现在，可以在 Azure 门户中查看此计算机，以查看[其公共 IP 地址与完全限定的域名](virtual-machines-windows-portal-sql-server-provision.md)。

## <a name="example-script"></a>示例脚本
以下脚本包含本教程的完整 PowerShell 脚本。 其中假设已将 Azure 订阅设置为配合使用 **Add-AzureRmAccount** 和 **Select-AzureRmSubscription** 命令。

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>后续步骤
创建虚拟机后，可以使用 RDP 和设置连接来连接虚拟机。 有关详细信息，请参阅 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager)](virtual-machines-windows-sql-connect.md)（连接到 Azure 上的 SQL Server 虚拟机 (Resource Manager)）。
