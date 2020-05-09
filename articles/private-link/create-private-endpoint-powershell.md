---
title: 使用 Azure PowerShell 创建 Azure 专用终结点 |Microsoft Docs
description: 了解 Azure 专用链接
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8af33e95c92cf51bdabe3325bd9249b4662b7d28
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583770"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>使用 Azure PowerShell 创建专用终结点
专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 

本快速入门介绍如何使用 Azure PowerShell 在 Azure 虚拟网络中创建一个 VM，以及一个包含 Azure 专用终结点的 SQL 数据库服务器。 然后，你可以从该 VM 安全访问该 SQL 数据库服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

创建资源之前，必须先创建一个资源组，该资源组使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 托管虚拟网络和专用终结点。 以下示例在 " *WestUS* " 位置创建名为 " *myResourceGroup* " 的资源组：

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
在本部分，请创建虚拟网络和子网。 接下来，请将子网关联到虚拟网络。

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 为专用终结点创建虚拟网络。 以下示例创建名为 *MyVirtualNetwork* 的虚拟网络：
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>添加子网

Azure 将资源部署到虚拟网络中的子网，因此需要创建子网。 使用 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) 创建名为“mySubnet”  的子网配置。 以下示例创建一个名为 *mySubnet* 的子网，并将专用终结点网络策略标志设置为“禁用”  。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> `PrivateEndpointNetworkPoliciesFlag` 参数很容易与另一可用标志（即 `PrivateLinkServiceNetworkPoliciesFlag`）混淆，因为它们都是长词，外观也类似。  请确保使用正确的，即 `PrivateEndpointNetworkPoliciesFlag`。

### <a name="associate-the-subnet-to-the-virtual-network"></a>将子网关联到虚拟网络

可以使用 [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) 将子网配置写入虚拟网络。 此命令创建子网：

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 在虚拟网络中创建 VM。 运行下一个命令时，系统会提示输入凭据。 为 VM 输入用户名和密码：

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

## <a name="create-a-sql-database-server"></a>创建 SQL 数据库服务器 

使用 New-AzSqlServer 命令创建 SQL 数据库服务器。 请记住，你的 SQL 数据库服务器名称必须在 Azure 中是唯一的，因此请将括号中的占位符值替换为你自己的唯一值：

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

虚拟网络中的 SQL 数据库服务器的专用终结点，使用 [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) 创建： 

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
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域 
为 SQL 数据库服务器域创建专用 DNS 区域，并创建一个与虚拟网络关联的链接： 

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

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 返回 VM 的公共 IP 地址。 此示例返回 myVM  VM 的公共 IP 地址：

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
在本地计算机上打开命令提示符。 运行 mstsc 命令。 将 <publicIpAddress> 替换为上一步骤中返回的公共 IP 地址： 


> [!NOTE]
> 如果在本地计算机上已从 PowerShell 提示符运行了这些命令，并且使用的是 Az PowerShell 模块 1.0 版本或更高版本，那么可以继续使用该接口。
```
mstsc /v:<publicIpAddress>
```

1. 出现提示时，选择“连接”  。 
2. 输入在创建 VM 时指定的用户名和密码。
  > [!NOTE]
  > 可能需要选择“更多选择”>“使用其他帐户”，以指定在创建 VM 时输入的凭据。 
  
3. 选择“确定”  。 
4. 可能会收到证书警告。 如果收到证书警告，选择“确定”或“继续”   。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

1. 在 myVM 的远程桌面中，打开 PowerShell。
2. 输入 `nslookup myserver.database.windows.net`。 请记得将`myserver`替换为您的 SQL server 名称。

    将收到类似于下面的消息：
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. 安装 SQL Server Management Studio。
4. 在“连接服务器”中，输入或选择以下信息  ：

    | 设置 | 值 |
    | --- | --- |
    | 服务器类型 | 数据库引擎 |
    | 服务器名称 | myserver.database.windows.net |
    | 用户名 | 输入创建过程中提供的用户名 |
    | Password | 输入在创建过程中提供的密码 |
    | 记住密码 | 是 |
    
5. 选择“连接”  。
6. 浏览左侧菜单中的 "**数据库**"。 
7. （可选）创建或查询 mydatabase 中的信息。
8. 关闭与 *myVM* 的远程桌面连接。 

## <a name="clean-up-resources"></a>清理资源 
使用专用终结点、SQL 数据库服务器和 VM 后，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组和组内所有资源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用链接](private-link-overview.md)
