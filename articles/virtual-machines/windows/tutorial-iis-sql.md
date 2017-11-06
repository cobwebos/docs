---
title: "在 Azure 中创建运行 SQL&#92;IIS&#92;.NET 堆栈的 VM | Microsoft Docs"
description: "教程 - 在 Windows 虚拟机上安装 Azure SQL、IIS、.NET 堆栈。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>在 Azure 中安装 SQL&#92;IIS&#92;.NET 堆栈

在本教程中，我们将使用 Azure PowerShell 安装 SQL&#92;IIS&#92;.NET 堆栈。 此堆栈包含两个运行 Windows Server 2016 的 VM，一个带有 IIS 和 .NET，另一个带有 SQL Server。

> [!div class="checklist"]
> * 使用 New-AzVM 创建 VM
> * 在 VM 上安装 IIS 和 .NET Core SDK
> * 创建运行 SQL Server 的 VM
> * 安装 SQL Server 扩展



## <a name="create-a-iis-vm"></a>创建 IIS VM 

在此示例中，我们使用 [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) cmdlet 在 PowerShell Cloud Shell 中快速创建 Windows Server 2016 VM，然后安装 IIS 和 .NET Framework。 IIS 和 SQL VM 共享资源组和虚拟网络，因此我们创建这些名称的变量。

单击代码块右上方的“试用”按钮，以便在此窗口中启动 Cloud Shell。 在命令提示符下将要求你提供虚拟机的凭据。

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

使用自定义脚本扩展安装 IIS 和 .NET framework。

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL VM

我们使用 SQL server 的预配置 Azure marketplace 映像创建 SQL VM。 首先创建 VM，然后在 VM 上安装 SQL Server 扩展。 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

使用 [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) 将 [SQL Server 扩展](/sql/virtual-machines-windows-sql-server-agent-extension.md)添加到 SQL VM。

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>后续步骤

在本教程中，已使用 Azure PowerShell 安装 SQL&#92;IIS&#92;.NET 堆栈。 你已了解如何：

> [!div class="checklist"]
> * 使用 New-AzVM 创建 VM
> * 在 VM 上安装 IIS 和 .NET Core SDK
> * 创建运行 SQL Server 的 VM
> * 安装 SQL Server 扩展

转到下一教程，了解如何使用 SSL 证书保护 IIS Web 服务器。

> [!div class="nextstepaction"]
> [使用 SSL 证书保护 IIS Web 服务器](tutorial-secure-web-server.md)

