---
title: 使用 Azure PowerShell 在 Windows 虚拟机上创建 SQL Server | Microsoft Docs
description: 本教程介绍如何使用 Azure PowerShell 创建运行 SQL Server 2017 的 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: eafbf102c092b180a1f3c882f5ae626e60b80f30
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514605"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 在 Windows 虚拟机上创建 SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本快速入门逐步讲解如何使用 Azure PowerShell 创建 SQL Server 虚拟机 (VM)。

> [!TIP]
> - 本快速入门提供的路径适用于快速预配并连接到 SQL VM。 若要详细了解创建 SQL VM 所需的其他 Azure PowerShell 选项，请参阅 [SQL Server VM 预配指南（使用 Azure PowerShell）](create-sql-vm-powershell.md)。
> - 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.md)。

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> 获取 Azure 订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="get-azure-powershell"></a><a id="powershell"></a> 获取 Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>配置 PowerShell

1. 打开 PowerShell，通过运行 **Connect-AzAccount** 命令建立对 Azure 帐户的访问。

   ```powershell
   Connect-AzAccount
   ```

1. 看到“登录”窗口时，请输入凭据。 使用登录 Azure 门户时所用的相同电子邮件和密码。

## <a name="create-a-resource-group"></a>创建资源组

1. 使用唯一的资源组名称定义一个变量。 为了简化本快速入门的其余部分，其余命令将此名称作为基础用于其他资源名称。

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. 为所有 VM 资源定义目标 Azure 区域的位置。

   ```powershell
   $Location = "East US"
   ```

1. 创建资源组。

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>配置网络设置

1. 创建虚拟网络、子网和公共 IP 地址。 这些资源用来与虚拟机建立网络连接，以及连接到 Internet。

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. 创建网络安全组。 配置允许远程桌面 (RDP) 和 SQL Server 连接的规则。

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. 创建网络接口。

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>创建 SQL VM

1. 定义登录到 VM 所需的凭据。 用户名为“azureadmin”。 请确保在运行命令之前更改 \<password>。

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. 创建虚拟机配置对象，然后创建 VM。 以下命令在 Windows Server 2016 上创建 SQL Server 2017 Developer Edition VM。

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > 创建 VM 需要几分钟时间。

## <a name="register-with-sql-vm-rp"></a>注册 SQL VM RP 

若要获取门户集成和 SQL VM 功能，必须注册 [SQL VM 资源提供程序](sql-vm-resource-provider-register.md)。

若要获取完整的功能，需要在完整模式下注册资源提供程序。 不过，这样做会重新启动 SQL Server 服务，因此建议的方法是在轻型模式下注册，然后在维护时段内升级到完整模式。 

首先，在轻型模式下注册 SQL Server VM： 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

然后，在维护时段内升级到完整模式： 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL VM resource provider in full mode
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>通过远程桌面连接到 VM

1. 使用以下命令检索新 VM 的公共 IP 地址。

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. 将返回的 IP 地址作为命令行参数传递给 **mstsc**，以便启动到新 VM 的远程桌面会话。

   ```
   mstsc /v:<publicIpAddress>
   ```

1. 出现输入凭据的提示时，请选择输入另一个帐户的凭据。 输入带前置反斜杠的用户名（例如 `\azureadmin`），以及此前在本快速入门中设置的密码。

## <a name="connect-to-sql-server"></a>连接到 SQL Server

1. 登录到远程桌面会话以后，从开始菜单启动 **SQL Server Management Studio 2017**。

1. 在“连接到服务器”对话框中，保留默认设置。 服务器名称是 VM 的名称。 身份验证设置为“Windows 身份验证”。 选择“连接”。

你现在已通过本地方式连接到 SQL Server。 若要进行远程连接，必须通过 Azure 门户或手动[配置连接性](ways-to-connect-to-sql.md)。

## <a name="clean-up-resources"></a>清理资源

如果不需要让 VM 持续运行，可以在不使用它时将它停止，以免产生不必要的费用。 以下命令可停止 VM，但会保留它供将来使用。

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

还可以使用 **Remove-AzResourceGroup** 命令永久删除与虚拟机关联的所有资源。 这样做也会永久删除虚拟机，因此请小心使用此命令。

## <a name="next-steps"></a>后续步骤

本快速入门使用 Azure PowerShell 创建了一个 SQL Server 2017 虚拟机。 若要详细了解如何将数据迁移到新的 SQL Server，请参阅以下文章。

> [!div class="nextstepaction"]
> [将数据库迁移到 SQL VM](migrate-to-vm-from-sql-server.md)
