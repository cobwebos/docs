---
title: Create an Azure Private Endpoint using Azure PowerShell| Microsoft Docs
description: Learn about Azure Private Link
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 83f1cbc3f8da61370c90744be3f0a7b230e016c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229408"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Create a private endpoint using Azure PowerShell
专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 

本快速入门介绍如何使用 Azure PowerShell 在 Azure 虚拟网络中创建一个 VM，以及一个包含 Azure 专用终结点的 SQL 数据库服务器。 然后，你可以从该 VM 安全访问该 SQL 数据库服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

Before you can create your resources, you must create a resource group that hosts the Virtual Network and the private endpoint with [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). The following example creates a resource group named *myResourceGroup* in the *WestUS* location:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
In this section, you create a virtual network and a subnet. Next, you associate the subnet to your Virtual Network.

### <a name="create-a-virtual-network"></a>创建虚拟网络

Create a Virtual network for your private endpoint with [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). The following example creates a Virtual Network named *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Add a Subnet

Azure deploys resources to a subnet within a Virtual Network, so you need to create a subnet. Create a subnet configuration named *mySubnet* with [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). The following example creates a subnet named *mySubnet* with the private endpoint network policy flag set to **Disabled**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associate the Subnet to the Virtual Network

You can write the subnet configuration to the Virtual Network with [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). 此命令创建子网：

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

Create a VM in the Virtual Network with [New-AzVM](/powershell/module/az.compute/new-azvm). 运行下一个命令时，系统会提示输入凭据。 为 VM 输入用户名和密码：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob` 选项在后台创建 VM。 可以继续执行下一步。

Azure 开始在后台创建 VM 时，将得到如下结果：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 

Create a  SQL Database Server by using the New-AzSqlServer command. Remember that the name of your SQL Database server must be unique across Azure, so replace the placeholder value in brackets with your own unique value:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>创建专用终结点

Private Endpoint for the SQL Database Server in your Virtual Network with [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a private DNS zone for SQL Database Server domain and create an association link with the virtual network: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 返回 VM 的公共 IP 地址。 This example returns the public IP address of the *myVM* VM:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
在本地计算机上打开命令提示符。 Run the mstsc command. 将 <publicIpAddress> 替换为上一步骤中返回的公共 IP 地址： 


> [!NOTE]
> 如果在本地计算机上已从 PowerShell 提示符运行了这些命令，并且使用的是 Az PowerShell 模块 1.0 版本或更高版本，那么可以继续使用该接口。
```
mstsc /v:<publicIpAddress>
```

1. 出现提示时，选择“连接”。 
2. 输入在创建 VM 时指定的用户名和密码。
  > [!NOTE]
  > You may need to select More choices > Use a different account, to specify the credentials you entered when you created the VM. 
  
3. 选择“确定”。 
4. 可能会收到证书警告。 如果收到证书警告，选择“确定”或“继续”。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

1. In the Remote Desktop of myVM, open PowerShell.
2. 输入 `nslookup myserver.database.windows.net` 。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information: Setting Value Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## <a name="clean-up-resources"></a>清理资源 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤
- Learn more about [Azure Private Link](private-link-overview.md)
