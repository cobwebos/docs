---
title: 使用 Azure PowerShell 预配 SQL Server VM 的指南 | Microsoft Docs
description: 提供了用于使用 SQL Server 虚拟机库映像创建 Azure VM 的步骤和 PowerShell 命令。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 072c58377645c807328bfcd79028daad70df7338
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102111"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>如何使用 Azure PowerShell 预配 SQL Server 虚拟机

本指南解释了使用 Azure PowerShell 创建 Windows SQL Server VM 的选项。 有关具有多个默认值的简明 Azure PowerShell 示例，请参阅 [SQL VM Azure PowerShell 快速入门](quickstart-sql-vm-create-powershell.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>配置订阅

1. 打开 PowerShell，通过运行 **Connect-AzAccount** 命令建立对 Azure 帐户的访问。

   ```powershell
   Connect-AzAccount
   ```

1. 此时应会显示用于输入凭据的屏幕。 使用登录 Azure 门户时所用的相同电子邮件和密码。

## <a name="define-image-variables"></a>定义映像变量
若要重复使用值并简化脚本创建，请先定义一些变量。 根据需要更改参数值，但在修改提供的值时，请注意与名称长度和特殊字符相关的命名限制。

### <a name="location-and-resource-group"></a>位置和资源组
定义数据区域，以及要在其中创建其他 VM 资源的资源组。

根据需要进行修改，然后运行这些 cmdlet 以初始化这些变量。

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>存储属性
定义存储帐户，以及虚拟机使用的存储类型。

根据需要进行修改，然后运行以下 cmdlet 以初始化这些变量。 我们建议将[高级 SSD](../disks-types.md#premium-ssd) 用于生产工作负载。

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>网络属性
在虚拟机中定义网络使用的属性。 

- 网络接口
- TCP/IP 分配方法
- 虚拟网络名称
- 虚拟子网名称
- 虚拟网络的 IP 地址范围
- 子网的 IP 地址范围
- 公共域名标签

根据需要进行修改，然后运行此 cmdlet 以初始化这些变量。

```powershell
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
定义虚拟机名称、计算机名称、虚拟机大小和虚拟机的操作系统磁盘名称。

根据需要进行修改，然后运行此 cmdlet 以初始化这些变量。

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>选择 SQL Server 映像

使用以下变量来定义要用于虚拟机的 SQL Server 映像。 

1. 首先，使用 `Get-AzVMImageOffer` 命令列出所有 SQL Server 映像套餐。 此命令将列出 Azure 门户中当前提供的映像，以及只能通过 PowerShell 安装的早期映像：

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. 对于本教程，请使用以下变量指定 Windows Server 2016 上的 SQL Server 2017。

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 接下来，列出套餐的可用版本。

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. 对于本教程，请使用 SQL Server 2017 Developer Edition (**SQLDEV**)。 Developer Edition 针对测试和开发自由授权，用户只需支付运行 VM 的成本。

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>创建资源组
在 Resource Manager 部署模型中，创建的第一个对象是资源组。 使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 Azure 资源组及其资源。 指定前面初始化的资源组名称和位置变量。

运行此 cmdlet 来创建新的资源组。

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>创建存储帐户
虚拟机需要使用存储资源来存储操作系统磁盘及 SQL Server 数据和日志文件。 为简单起见，我们将为两者创建单个磁盘。 稍后可以使用 [Add-Azure Disk](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) cmdlet 来附加其他磁盘，以便将 SQL Server 数据和日志文件放在专用磁盘上。 使用 [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet 在新资源组中创建标准存储帐户。 指定前面初始化的存储帐户名称、存储 SKU 名称和位置变量。

运行此 cmdlet 来创建新的存储帐户。

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
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
首先创建虚拟网络的子网配置。 本教程使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet 创建默认子网。 指定前面初始化的子网名称和地址前缀变量。

> [!NOTE]
> 可以使用此 cmdlet 来定义虚拟网络子网配置的其他属性，但这已超出本教程的范围。

运行此 cmdlet 来创建虚拟子网配置。

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>创建虚拟网络
接下来，使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) cmdlet 在新资源组中创建虚拟网络。 指定前面初始化的名称、位置和地址前缀变量。 使用在上一步骤中定义的子网配置。

运行此 cmdlet 来创建虚拟网络。

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>创建公共 IP 地址
定义虚拟网络后，必须配置 IP 地址才能连接到虚拟机。 本教程使用动态 IP 地址创建公共 IP 地址，以支持 Internet 连接。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) cmdlet 在新资源组中创建公共 IP 地址。 指定前面初始化的名称、位置、分配方法和 DNS 域名标签变量。

> [!NOTE]
> 可以使用此 cmdlet 来定义公共 IP 地址的其他属性，但这已超出本初步教程的范围。 也可以创建专用地址或具有静态地址的地址，但这也超出了本教程的范围。

运行此 cmdlet 来创建公共 IP 地址。

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>创建网络安全组
为确保 VM 和 SQL Server 流量安全，请创建网络安全组。

1. 首先，为 RDP 创建网络安全组规则以允许远程桌面连接。

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. 配置一个网络安全组规则以允许 TCP 端口 1433 上的流量。 这样就可以通过 Internet 连接到 SQL Server。

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. 创建网络安全组。

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>创建网络接口
现在，可为虚拟机创建网络接口。 使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) cmdlet 在新资源组中创建网络接口。 指定前面定义的名称、位置、子网和公共 IP 地址。

运行此 cmdlet 来创建网络接口。

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>配置 VM 对象
现已定义存储和网络资源，接下来可以定义虚拟机的计算资源。

- 指定虚拟机大小和各项操作系统属性。
- 指定前面创建的网络接口。
- 定义 Blob 存储。
- 指定操作系统磁盘。

### <a name="create-the-vm-object"></a>创建 VM 对象
首先指定虚拟机大小。 本教程指定了 DS13。 使用 [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) cmdlet 创建可配置的虚拟机对象。 指定前面初始化的名称和大小变量。

运行此 cmdlet 来创建虚拟机对象。

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>创建一个凭据对象，以保留本地管理员凭据的名称和密码
必须先提供本地管理员帐户的凭据作为安全字符串，才能设置虚拟机的操作系统属性。 为实现此目的，请使用 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet。

运行以下 cmdlet，然后在 PowerShell 凭据请求窗口中，键入虚拟机中本地管理员帐户使用的名称和密码。

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统属性
现在，可以使用 [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet 设置虚拟机的操作系统属性。

- 将操作系统类型设置为 Windows。
- 要求安装[虚拟机代理](../../extensions/agent-windows.md)。
- 指定 cmdlet 要启用自动更新。
- 指定前面初始化的虚拟机名称、计算机名和凭据变量。

运行此 cmdlet 来设置虚拟机的操作系统属性。

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>将网络接口添加到虚拟机
接下来，运行 [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) cmdlet，以使用前面定义的变量来添加网络接口。

运行此 cmdlet 来设置虚拟机的网络接口。

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>设置虚拟机所要使用的磁盘的 Blob 存储位置
接下来，使用前面定义的变量设置 VM 磁盘的 Blob 存储位置。

运行此 cmdlet 来设置 Blob 存储位置。

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>设置虚拟机的操作系统磁盘属性
接下来，使用 [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) cmdlet 设置虚拟机的操作系统磁盘属性。 

- 指定来自映像的虚拟机的操作系统。
- 将缓存设置为只读（因为 SQL Server 将安装在同一磁盘上）。
- 指定前面初始化的 VM 名称和操作系统磁盘变量。

运行此 cmdlet 来设置虚拟机的操作系统磁盘属性。

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>指定虚拟机的平台映像
最后一个配置步骤是指定虚拟机的平台映像。 本教程使用最新的 SQL Server 2016 CTP 映像。 运行 [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) cmdlet，以通过前面定义的变量来使用此映像。

运行此 cmdlet 来指定虚拟机的平台映像。

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>创建 SQL VM
完成配置步骤后，接下来可以创建虚拟机。 运行 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet，以使用你定义的变量来创建虚拟机。

> [!TIP]
> 创建 VM 可能需要几分钟时间。

运行此 cmdlet 来创建虚拟机。

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

虚拟机已创建。

> [!NOTE]
> 如果收到有关启动诊断的错误，可将其忽略。 由于为虚拟机磁盘指定的存储帐户是高级存储帐户，因此会创建标准存储帐户用于启动诊断。

## <a name="install-the-sql-iaas-agent"></a>安装 SQL IaaS 代理
SQL Server 虚拟机通过 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)支持自动化管理功能。 若要在新 VM 上安装该代理，请在创建 VM 后运行以下命令。


   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>停止或删除 VM

如果不需要让 VM 持续运行，可以在不使用它时将它停止，以免产生不必要的费用。 以下命令可停止 VM，但保留它供将来使用。

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

还可以使用 **Remove-AzResourceGroup** 命令永久删除与虚拟机关联的所有资源。 这样做也会永久删除虚拟机，因此请小心使用此命令。

## <a name="example-script"></a>示例脚本
以下脚本包含本教程的完整 PowerShell 脚本。 它假设已将 Azure 订阅设置为配合使用 **Connect-AzAccount** 和 **Select-AzSubscription** 命令。

```powershell
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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>后续步骤
在创建虚拟机后，可以：

- 使用 RDP 连接到虚拟机
- 在门户中配置 VM 的 SQL Server 设置，包括：
   - [存储设置](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [自动化管理任务](virtual-machines-windows-sql-server-agent-extension.md)
- [配置连接](virtual-machines-windows-sql-connect.md)
- 将客户端和应用程序连接到新的 SQL Server 实例

