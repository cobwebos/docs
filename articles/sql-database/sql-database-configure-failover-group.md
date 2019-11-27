---
title: 配置故障转移组
description: 了解如何使用 Azure 门户、Az CLI 和 PowerShell 为 Azure SQL 数据库单一数据库、弹性池和托管实例配置自动故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: fb9ee2378679c420a7675856ec95e60f6ae1d14f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827139"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>为 Azure SQL 数据库配置故障转移组

本主题讲解如何使用 Azure 门户或 PowerShell 为 Azure SQL 数据库单一数据库、弹性池和托管实例配置[自动故障转移组](sql-database-auto-failover-group.md)。 

## <a name="single-database"></a>单一数据库
使用 Azure 门户或 PowerShell 创建故障转移组，并将单一数据库添加到其中。

### <a name="prerequisites"></a>先决条件

请注意以下先决条件：

- 辅助服务器的服务器登录名和防火墙设置必须与主服务器相匹配。 

### <a name="create-failover-group"></a>创建故障转移组

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户创建故障转移组，并将单一数据库添加到其中。

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择要添加到故障转移组中的单一数据库。 
1. 在**服务器名称**下选择服务器的名称以打开服务器的设置。

   ![打开单一数据库的服务器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择“添加组”以创建新的故障转移组。 

    ![添加新的故障转移组](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在“故障转移组”页上输入或选择所需的值，然后选择“创建”。

   - **组中的数据库**：选择要添加到故障转移组的数据库。 将数据库添加到故障转移组的操作会自动启动异地复制过程。 
        
    ![将 SQL 数据库添加到故障转移组](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 创建故障转移组，并将单一数据库添加到其中。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>测试故障转移 

使用 Azure 门户或 PowerShell 测试故障转移组的故障转移。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户测试故障转移组的故障转移。 

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择要添加到故障转移组中的单一数据库。 

   ![打开单一数据库的服务器](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择刚刚创建的故障转移组。 
  
   ![在门户中选择故障转移组](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 
1. 在“任务”窗格中选择“故障转移”，以故障转移包含你的单一数据库的故障转移组。 
1. 在告知将会断开 TDS 会话连接的警告中选择“是”。 

   ![故障转移包含 SQL 数据库的故障转移组](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 查看哪个服务器现在是主服务器，哪个服务器是辅助服务器。 如果故障转移成功，这两个服务器的角色应会交换。 
1. 再次选择“故障转移”以将服务器故障回复到其初始角色。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移组的故障转移。  

检查辅助副本的角色： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
故障转移到辅助服务器： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

将故障转移组还原为主服务器：

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>弹性池
使用 Azure 门户或 PowerShell 创建故障转移组，并将弹性池添加到其中。  

### <a name="prerequisites"></a>先决条件

请注意以下先决条件：

- 辅助服务器的服务器登录名和防火墙设置必须与主服务器相匹配。 

### <a name="create-the-failover-group"></a>创建故障转移组 

使用 Azure 门户或 PowerShell 为弹性池创建故障转移组。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户创建故障转移组，并将弹性池添加到其中。

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择要添加到故障转移组中的弹性池。 
1. 在“概述”窗格上，选择**服务器名称**下的服务器名称以打开服务器的设置。
  
    ![打开弹性池的服务器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择“添加组”以创建新的故障转移组。 

    ![添加新的故障转移组](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在“故障转移组”页上输入或选择所需的值，然后选择“创建”。 创建新的辅助服务器，或选择现有的辅助服务器。 

1. 选择“组中的数据库”，然后选择要添加到故障转移组中的弹性池。 如果辅助服务器上没有弹性池，则会出现一条警告，提示你在辅助服务器上创建弹性池。 选择该警告，然后选择“确定”以在辅助服务器上创建弹性池。 
        
    ![将弹性池添加到故障转移组中](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 选择“选择”以将弹性池设置应用到故障转移组，然后选择“创建”以创建故障转移组。 将弹性池添加到故障转移组的操作会自动启动异地复制过程。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建故障转移组，并将弹性池添加到其中。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>测试故障转移

使用 Azure 门户或 PowerShell 测试弹性池的故障转移。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。 

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择要添加到故障转移组中的弹性池。 
1. 在“概述”窗格上，选择**服务器名称**下的服务器名称以打开服务器的设置。
  
    ![打开弹性池的服务器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. 在“设置”窗格下选择“故障转移组”，然后选择在第 2 部分创建的故障转移组。 
  
   ![在门户中选择故障转移组](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 
1. 在“任务”窗格中选择“故障转移”，以故障转移包含弹性池的故障转移组。 
1. 在告知将会断开 TDS 会话连接的警告中选择“是”。 

   ![故障转移包含 SQL 数据库的故障转移组](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 如果故障转移成功，这两个服务器的角色应会交换。 
1. 再次选择“故障转移”，将故障转移组故障回复到原始设置。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移组的故障转移。

检查辅助副本的角色： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

故障转移到辅助服务器： 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>托管实例

使用 Azure 门户或 PowerShell 在两个托管实例之间创建故障转移组。 

需要为每个托管实例的虚拟网络创建一个网关，连接两个网关，然后创建故障转移组。

### <a name="prerequisites"></a>先决条件
请注意以下先决条件：

- 辅助托管实例必须为空。
- 辅助虚拟网络的子网范围不得与主虚拟网络的子网范围重叠。 
- 辅助实例的排序规则和时区必须与主实例的排序规则和时区相匹配。 
- 连接两个网关时，两个连接的**共享密钥**应该相同。 

### <a name="create-primary-virtual-network-gateway"></a>创建主虚拟网络网关 

使用 Azure 门户或 PowerShell 创建主虚拟网络网关。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建主虚拟网络网关。 

1. 在 [Azure 门户](https://portal.azure.com)中转到你的资源组，并选择主托管实例的“虚拟网络”资源。 
1. 在“设置”下选择“子网”，然后选择添加新的“网关子网”。 保留默认值。 

   ![为主托管实例添加网关](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 创建子网网关后，在左侧导航窗格中选择“创建资源”，然后在搜索框中键入 **。** `Virtual network gateway` 选择“Microsoft”发布的“虚拟网络网关”资源。 

   ![创建新的虚拟网络网关](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填写必填字段，为主托管实例配置网关。 

   下表显示了主托管实例的网关所需的值：
 
    | **字段** | 值 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **名称** | 虚拟网络网关的名称。 | 
    | **区域** | 辅助托管实例所在的区域。 |
    | **网关类型** | 选择“VPN”。 |
    | **VPN 类型** | 选择“基于路由” |
    | **SKU**| 保留默认值 `VpnGw1`。 |
    | **Location**| 辅助托管实例和辅助虚拟网络所在的位置。   |
    | **虚拟网络**| 为辅助托管实例选择虚拟网络。 |
    | **公共 IP 地址**| 选择“新建”。 |
    | **公共 IP 地址名称**| 输入 IP 地址的名称。 |
    | &nbsp; | &nbsp; |

1. 将其他值保留为默认值，然后选择“查看 + 创建”以检查虚拟网络网关的设置。

   ![主网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 选择“创建”以创建新的虚拟网络网关。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建主虚拟网络网关。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>创建辅助虚拟网络网关

使用 Azure 门户或 PowerShell 创建辅助虚拟网络网关。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
重复上一部分中的步骤，为辅助托管实例创建虚拟网络子网和网关。 填写必填字段，为辅助托管实例配置网关。 

   下表显示了辅助托管实例的网关所需的值：

   | **字段** | 值 |
   | --- | --- |
   | **订阅** |  辅助托管实例所在的订阅。 |
   | **名称** | 虚拟网络网关的名称，例如 `secondary-mi-gateway`。 | 
   | **区域** | 辅助托管实例所在的区域。 |
   | **网关类型** | 选择“VPN”。 |
   | **VPN 类型** | 选择“基于路由” |
   | **SKU**| 保留默认值 `VpnGw1`。 |
   | **Location**| 辅助托管实例和辅助虚拟网络所在的位置。   |
   | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-secondary`。 |
   | **公共 IP 地址**| 选择“新建”。 |
   | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![辅助网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建辅助虚拟网络网关。 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>连接网关 
使用 Azure 门户或 PowerShell 在两个网关之间创建连接。 

需要创建的两个连接 - 从主网关到辅助网关的连接，以及从辅助网关到主网关的连接。 

对两个连接使用的共享密钥应该相同。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户在两个网关之间创建连接。 

1. 从 " [Azure 门户](https://portal.azure.com)中选择"**创建资源**"。
1. 在 "搜索" 框中键入 `connection`，然后按 enter 搜索，这会将你转到 Microsoft 发布的**连接**资源。
1. 选择 "**创建**" 以创建连接。 
1. 在 "**基本**信息" 选项卡上，选择以下值，然后选择 **"确定"** 。 
    1. 为**连接类型**选择 `VNet-to-VNet`。 
    1. 从下拉列表中选择订阅。 
    1. 在下拉中选择托管实例的资源组。 
    1. 从下拉位置中选择主托管实例的位置 
1. 在 "**设置**" 选项卡上，选择或输入以下值，然后选择 **"确定"** ：
    1. 选择**第一个虚拟网络网关**的主网络网关，如 `Primary-Gateway`。  
    1. 为**第二个虚拟网络网关**（如 `Secondary-Gateway`）选择辅助网络网关。 
    1. 选中 "**建立双向连接**" 旁边的复选框。 
    1. 保留默认的主连接名称，或将其重命名为你选择的值。 
    1. 提供连接的**共享密钥（PSK）** ，如 `mi1m2psk`。 

   ![创建网关连接](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. 在 "**摘要**" 选项卡上，查看双向连接的设置，然后选择 **"确定"** 以创建连接。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 在两个网关之间创建连接。 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>创建故障转移组 
使用 Azure 门户或 PowerShell 为托管实例创建故障转移组。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户为托管实例创建故障转移组。 

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择要添加到故障转移组中的主托管实例。  
1. 在“设置”下，导航到“实例故障转移组”，然后选择“添加组”打开“实例故障转移组”页。 

   ![添加故障转移组](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在“实例故障转移组”页上键入故障转移组的名称，然后从下拉列表中选择辅助托管实例。 选择“创建”以创建故障转移组。 

   ![创建故障转移组](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 故障转移组部署完成后，你将返回到“故障转移组”页。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 为托管实例创建故障转移组。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>测试故障转移

使用 Azure 门户或 PowerShell 测试故障转移组的故障转移。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户测试故障转移组的故障转移。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的托管实例，然后在“设置”下选择“实例故障转移组”。 
1. 查看哪个托管实例是主实例，哪个托管实例是辅助实例。 
1. 选择“故障转移”，然后在有关正在断开 TDS 会话的警告中选择“是”。 

   ![将故障转移组故障转移](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪个托管实例是主实例，哪个实例是辅助实例。 如果故障转移成功，这两个实例的角色应会交换。 

   ![故障转移后托管实例的角色已交换](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 再次选择“故障转移”，将主实例故障回复为主角色。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移组的故障转移。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>定位侦听器终结点

配置故障转移组后，将应用程序的连接字符串更新为侦听器终结点。 这会使应用程序保持连接到故障转移组侦听器，而不是连接到主数据库、弹性池或托管实例。 这样，就不必要在每次 Azure SQL 数据库实体发生故障转移时手动更新连接字符串，并且流量将路由到当前充当主实体的任何实体。 

侦听器终结点采用 `fog-name.database.windows.net` 格式，查看故障转移组时，它会显示在 Azure 门户中：

![故障转移组连接字符串](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>备注

- 删除单一数据库或共用数据库的故障转移组不会停止复制，也不会删除已复制的数据库。 如果要在删除故障转移组后将单个或共用数据库添加回故障转移组，则需要手动停止异地复制并从辅助服务器中删除该数据库。 尝试将数据库添加到故障转移组时，不执行上述两项操作可能会导致类似于 `The operation cannot be performed due to multiple errors` 的错误。 


## <a name="next-steps"></a>后续步骤

有关配置故障转移组的详细步骤，请参阅以下教程：
- [将单一数据库添加到故障转移组](sql-database-single-database-failover-group-tutorial.md)
- [将弹性池添加到故障转移组](sql-database-elastic-pool-failover-group-tutorial.md)
- [将托管实例添加到故障转移组](sql-database-managed-instance-failover-group-tutorial.md)
 
有关 Azure SQL 数据库高可用性选项的概述，请参阅[异地复制](sql-database-active-geo-replication.md)和[自动故障转移组](sql-database-auto-failover-group.md)。 
