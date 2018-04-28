---
title: 使用 Azure PowerShell 预配 SQL Server VM 的指南 | Microsoft Docs
description: 提供了用于使用 SQL Server 虚拟机库映像创建 Azure VM 的步骤和 PowerShell 命令。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 7dff9fd736b1b0c616ee2d4f2591d632345156b9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>如何使用 Azure PowerShell 预配 SQL Server 虚拟机

本指南解释了使用 Azure PowerShell 创建 Windows SQL Server VM 的选项。 有关具有多个默认值的简明 Azure PowerShell 示例，请参阅 [SQL VM Azure PowerShell 快速入门](quickstart-sql-vm-create-powershell.md)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本文需要 Azure PowerShell 模块 3.6 版或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="configure-your-subscription"></a>配置订阅

1. 打开 PowerShell，通过运行 **Connect-AzureRmAccount** 命令建立对 Azure 帐户的访问。

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. 应当会出现用来输入凭据的登录屏幕。 使用登录 Azure 门户时所用的相同电子邮件和密码。

## <a name="define-image-variables"></a>定义映像变量
若要简化重复使用和脚本创建，请先定义一些变量。 根据需要更改参数值，但在修改提供的值时，请注意与名称长度和特殊字符相关的命名限制。

### <a name="location-and-resource-group"></a>位置和资源组
使用两个变量来定义数据区域，以及会在其中为虚拟机创建其他资源的资源组。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>存储属性
使用以下变量来定义存储帐户和虚拟机所要使用的存储类型。

根据需要修改并执行以下 cmdlet 来初始化这些变量。 请注意，在本示例中，我们将使用建议用于生产工作负荷的[高级存储](../premium-storage.md)。

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>网络属性
使用以下变量来定义网络接口、TCP/IP 分配方法、虚拟网络名称、虚拟子网名称、虚拟网络的 IP 地址范围、子网的 IP 地址范围，以及虚拟机中的网络所要使用的公共域名标签。

根据需要修改并执行以下 cmdlet 来初始化这些变量。

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
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

### <a name="choose-a-sql-server-image"></a>选择 SQL Server 映像
使用以下变量来定义要用于虚拟机的 SQL Server 映像。

1. 首先，使用 **Get-AzureRmVMImageOffer** 命令列出所有 SQL Server 映像产品：

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. 对于本教程，请使用以下变量指定 Windows Server 2016 上的 SQL Server 2017。

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 接下来，列出你的产品的可用版本。

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. 对于本教程，请使用 SQL Server 2017 Developer Edition (**SQLDEV**)。 Developer Edition 针对测试和开发自由授权，用户只需支付运行 VM 的成本。

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>创建资源组
在 Resource Manager 部署模型中，创建的第一个对象是资源组。 使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet 以前面初始化的变量所定义的资源组名称和位置来创建 Azure 资源组及其资源。

执行以下 cmdlet 来创建新的资源组。

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>创建存储帐户
虚拟机需要使用存储资源来存储操作系统磁盘及 SQL Server 数据和日志文件。 为简单起见，我们为两者创建单个磁盘。 稍后可以使用 [Add-Azure Disk](/powershell/module/azure/add-azuredisk) cmdlet 来附加其他磁盘，以便将 SQL Server 数据和日志文件放在专用磁盘上。 使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet，以前面初始化的变量所定义的存储帐户名称、存储 SKU 名称和位置在新资源组中创建一个标准存储帐户。

执行以下 cmdlet 来创建新的存储帐户。

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> 创建存储帐户可能需要花费几分钟时间。

## <a name="create-network-resources"></a>创建网络资源
虚拟机需要使用多个网络资源来建立网络连接。

* 每个虚拟机需要一个虚拟网络。
* 必须为每个虚拟网络至少定义一个子网。
* 必须使用公共或专用 IP 地址定义网络接口。

### <a name="create-a-virtual-network-subnet-configuration"></a>创建虚拟网络子网配置
首先创建虚拟网络的子网配置。 在本教程中，我们将使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet 创建默认子网。 我们将使用前面初始化的变量所定义的子网名称和地址前缀来创建虚拟网络子网配置。

> [!NOTE]
> 可以使用此 cmdlet 来定义虚拟网络子网配置的其他属性，但这已超出本教程的范围。

执行以下 cmdlet 来创建虚拟子网配置。

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>创建虚拟网络
接下来，使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet 创建虚拟网络。 使用前面初始化的变量所定义的名称、位置和地址前缀，并使用前一步骤中定义的子网配置，在新的资源组中创建虚拟网络。

执行以下 cmdlet 来创建虚拟网络。

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>创建公共 IP 地址
我们现已定义虚拟网络，接下来需要配置 IP 地址才能连接到虚拟机。 在本教程中，我们将使用动态 IP 地址来创建公共 IP 地址，以支持 Internet 连接。 使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet，以前面初始化的变量所定义的名称、位置、分配方法和 DNS 域名标签，在前面创建的资源组中创建公共 IP 地址。

> [!NOTE]
> 可以使用此 cmdlet 来定义公共 IP 地址的其他属性，但这已超出本初步教程的范围。 也可以创建专用地址或具有静态地址的地址，但这也超出了本教程的范围。

执行以下 cmdlet 来创建公共 IP 地址。

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>创建网络安全组
为确保 VM 和 SQL Server 流量安全，请创建网络安全组。

1. 首先，为 RDP 创建网络安全组规则以允许远程桌面连接。

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. 配置一个网络安全组规则以允许 TCP 端口 1433 上的流量。 这将启用通过 Internet 到 SQL Server 的连接。

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. 然后，创建网络安全组。

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>创建网络接口
我们现在已准备好创建虚拟机将要使用的网络接口。 我们将调用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) cmdlet，以前面定义的名称、位置、子网和公共 IP 地址，在前面创建的资源组中创建网络接口。

执行以下 cmdlet 来创建网络接口。

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>配置 VM 对象
我们现已定义存储和网络资源，接下来可以定义虚拟机的计算资源。 在本教程中，我们将指定虚拟机大小和各种操作系统属性、指定我们前面创建的网络接口、定义 Blob 存储，然后指定操作系统磁盘。

### <a name="create-the-vm-object"></a>创建 VM 对象
首先指定虚拟机大小。 对于本教程，我们应指定 DS13。 我们将调用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) cmdlet，以前面初始化的变量所定义的名称与大小来创建可配置的虚拟机对象。

执行以下 cmdlet 来创建虚拟机对象。

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>创建一个凭据对象，以保留本地管理员凭据的名称和密码
我们需要先提供本地管理员帐户的凭据作为安全字符串，才可以设置虚拟机的操作系统属性。 为实现此目的，请使用 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet。

执行以下 cmdlet，在 PowerShell 凭据请求窗口中，键入要用于虚拟机中本地管理员帐户的名称和密码。

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统属性
现在，我们可以使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet 设置虚拟机的操作系统属性了，将操作系统的类型设置为 Windows，要求安装[虚拟机代理](../agent-user-guide.md)，指定该 cmdlet 允许使用前面初始化的变量自动更新和设置虚拟机名称、计算机名称和凭据。

执行以下 cmdlet 来设置虚拟机的操作系统属性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>将网络接口添加到虚拟机
接下来，我们将前面创建的网络接口添加到虚拟机。 调用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet 来使用前面定义的网络接口变量添加网络接口。

执行以下 cmdlet 来设置虚拟机的网络接口。

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>设置虚拟机所要使用的磁盘的 Blob 存储位置
接下来，使用前面定义的变量，设置虚拟机所要使用的磁盘的 Blob 存储位置。

执行以下 cmdlet 来设置 Blob 存储位置。

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统磁盘属性
接下来，设置虚拟机的操作系统磁盘属性。 使用 [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) cmdlet 指定虚拟机的操作系统来自某个映像、将缓存设置为只读（因为要在同一磁盘上安装 SQL Server），并使用前面定义的变量来定义虚拟机名称和操作系统磁盘。

执行以下 cmdlet 来设置虚拟机的操作系统磁盘属性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>指定虚拟机的平台映像
最后一个配置步骤是指定虚拟机的平台映像。 在本教程中，我们使用最新的 SQL Server 2016 CTP 映像。 请通过 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet 根据之前定义的变量来使用此映像。

执行以下 cmdlet 来指定虚拟机的平台映像。

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>创建 SQL VM
现在已完成配置步骤，接下来可以创建虚拟机。 通过 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 使用我们已定义的变量来创建虚拟机。

执行以下 cmdlet 来创建虚拟机。

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

虚拟机已创建。

> [!NOTE]
> 可以忽略有关启动诊断的错误。 由于为虚拟机磁盘指定的存储帐户是高级存储帐户，因此会创建标准存储帐户用于启动诊断。

## <a name="install-the-sql-iaas-agent"></a>安装 SQL IaaS 代理
SQL Server 虚拟机通过 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)支持自动化管理功能。 若要在新 VM 上安装该代理，请在创建 VM 后运行以下命令。

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>删除测试 VM

如果不需要让 VM 继续运行，可以在不使用它时将它停止，以免产生不必要的费用。 以下命令可停止 VM，但保留它供将来使用。

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

还可以使用 **Remove-AzureRmResourceGroup** 命令永久删除与虚拟机关联的所有资源。 请小心使用此命令，因为它也会永久删除该虚拟机。

## <a name="example-script"></a>示例脚本
以下脚本包含本教程的完整 PowerShell 脚本。 其中假设已将 Azure 订阅设置为配合使用 **Connect-AzureRmAccount** 和 **Select-AzureRmSubscription** 命令。

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>后续步骤
在创建虚拟机后，可以：

- 使用远程桌面 (RDP) 连接到虚拟机。
- 在门户中配置 VM 的 SQL Server 设置，包括：
   - [存储设置](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [自动化管理任务](virtual-machines-windows-sql-server-agent-extension.md)
- [配置连接](virtual-machines-windows-sql-connect.md)。
- 将客户端和应用程序连接到新的 SQL Server 实例。

