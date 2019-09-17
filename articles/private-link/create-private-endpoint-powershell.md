---
title: 使用 Azure PowerShell 创建 Azure 专用终结点 |Microsoft Docs
description: 了解 Azure 专用链接
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: f9a2bd4c4ec176e018948a7a5a01603d075a7ea2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018005"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>使用 Azure PowerShell 创建专用终结点
专用终结点是 Azure 中专用链接的基本构建基块。 它使 Azure 资源（例如虚拟机）能够与专用链接资源进行私下通信。 

在本快速入门中，你将了解如何在 Azure 虚拟网络上创建虚拟机，以及如何使用 Azure PowerShell 的 Azure 专用终结点在 SQL 数据库服务器上创建 VM。 然后，可以从 VM 安全地访问 SQL 数据库服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

创建资源之前，必须先创建一个资源组，该资源组将承载虚拟网络和具有[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)的专用终结点。 以下示例在 " *WestUS* " 位置创建名为 " *myResourceGroup* " 的资源组：

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
在本部分中，将创建一个虚拟网络和一个子网。 接下来，将子网关联到虚拟网络。

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)为专用终结点创建虚拟网络。 以下示例创建名为*MyVirtualNetwork*的虚拟网络：
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>添加子网

Azure 会将资源部署到虚拟网络中的子网，因此需要创建一个子网。 使用 [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)创建名为 *mySubnet* 的子网配置。 以下示例创建名为*mySubnet*的子网，并将 "专用终结点网络策略" 标志设置为 "**已禁用**"。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet ` 
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>将子网关联到虚拟网络

可以通过 [AzVirtualNetwork 将](/powershell/module/az.network/Set-azVirtualNetwork)子网配置写入虚拟网络。 此命令创建子网：

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用[new-azvm](/powershell/module/az.compute/new-azvm)在虚拟网络中创建 VM。 运行下一个命令时，系统会提示输入凭据。 为 VM 输入用户名和密码：

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

使用 AzSqlServer 命令创建 SQL 数据库服务器。 请记住，SQL 数据库服务器的名称必须在 Azure 中是唯一的，因此请将占位符中的占位符值替换为你自己的唯一值：

$adminSqlLogin = "SqlAdmin" $password = "ChangeYourAdminPassword1"

$server = AzSqlServer-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" ` -Location "WestCentralUS" '-SqlAdministratorCredentials $ （New--ArgumentList $adminSqlLogin，$ （Convertto-html-SecureString-String $password-AsPlainText））

AzSqlDatabase-ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"` -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" ` -SampleName "AdventureWorksLT"


## <a name="create-a-private-endpoint"></a>创建专用终结点

具有[AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)的虚拟网络中的 SQL 数据库服务器的专用终结点： 

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

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域 
为 SQL 数据库服务器域创建专用 DNS 区域，并创建与虚拟网络的关联链接： 

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

使用 [AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 返回 VM 的公共 IP 地址。 此示例返回 *myVM* VM 的公共 IP 地址：

```azurepowershell
Get-AzPublicIpAddress ` 
  -Name myPublicIpAddress ` 
  -ResourceGroupName myResourceGroup ` 
  | Select IpAddress 
```  
在本地计算机上打开命令提示符。 运行 mstsc 命令。 将 <publicIpAddress>替换 为上一步返回的公共 IP 地址： 


> [!NOTE]
> 如果在本地计算机上已从 PowerShell 提示符运行了这些命令，并且使用的是 Az PowerShell 模块 1.0 版本或更高版本，那么可以继续使用该接口。
```
mstsc /v:<publicIpAddress>
```

1. 如果系统提示，请选择 " **连接**"。 
2. 输入在创建 VM 时指定的用户名和密码。
  > [!NOTE]
  > 你可能需要选择 "更多选项" > "使用其他帐户"，以便指定在创建 VM 时输入的凭据。 
3. 选择 **"确定"** 。 
4. 可能会收到证书警告。 如果这样做，请选择 **"是" 或 "**   **继续**"。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>从 VM 中私下访问 SQL 数据库服务器

1. 在 myVM 的远程桌面中，打开 PowerShell。
2. Enternslookup myserver.database.windows.net 您将收到类似于下面的消息：Azure PowerShellCopy 服务器：未知地址：168.63.129.16 非权威应答：名称： myserver.privatelink.database.windows.net 地址：10.0.0.5 别名： myserver.database.windows.net
3. 安装 SQL Server Management Studio
4. 在 "连接到服务器" 中，输入或选择以下信息：设置值服务器类型选择数据库引擎。
      服务器名称选择 "myserver.database.windows.net 用户名" 输入创建过程中提供的用户名。
      密码输入创建过程中提供的密码。
      记住密码选择是。
5. 选择 "连接"。
6. 浏览左侧菜单中的数据库。 
7. 同时创建或查询来自 mydatabase 的信息
8. 关闭与 *myVM*的远程桌面连接。 

## <a name="clean-up-resources"></a>清理资源 
使用完专用终结点、SQL 数据库服务器和 VM 后，请使用[AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)删除该资源组及其包含的所有资源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤
- 了解有关[Azure 专用链接](private-link-overview.md)的详细信息
