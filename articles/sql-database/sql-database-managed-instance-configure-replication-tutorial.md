---
title: 在两个托管实例和 SQL Server 之间配置事务复制
description: 本教程在 Azure VM 上配置发布服务器托管实例、分发服务器托管实例和 SQL Server 订阅服务器之间的复制，以及必要的网络组件，如专有 DNS 区域和 VPN 对等互连。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: f61c99db3f2c2569705e3a2df4b7134fdd29065a
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614907"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>教程：在两个托管实例和 SQL Server 之间配置事务复制


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 将托管实例配置为复制发布服务器。 
> - 将托管实例配置为复制分发服务器。 
> - 将 SQL Server 配置为订阅服务器。 

![SQL MI 发布服务器、SQL MI 分发服务器和 SQL Server 订阅服务器之间的复制](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

本教程适用于经验丰富的受众，并假定用户熟悉如何在 Azure 中部署和连接到这两个托管实例和 SQL Server VM。 因此本教程中略去了某些步骤。 

有关详细信息，请参阅 [Azure SQL 数据库托管实例概述](sql-database-managed-instance-index.yml)、[功能](sql-database-managed-instance.md)和[SQL 事务复制](sql-database-managed-instance-transactional-replication.md)文章。

若要配置托管实例发布服务器和托管实例订阅服务器之间的复制，请参阅[在两个托管实例之间配置事务复制](replication-with-sql-database-managed-instance.md)。 

## <a name="prerequisites"></a>必备条件

若要完成本教程，请确保具备以下先决条件：

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。 
- 尝试在同一虚拟网络中部署两个托管实例。 
- SQL Server 订阅服务器（本地或 Azure VM）。 本教程使用 Azure VM。  
- [SQL Server Management Studio (SSMS) 18.0 或更高版](/sql/ssms/download-sql-server-management-studio-ssms)。
- 最新版本的 [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)。
- 端口 445 和 1433 允许 Azure 防火墙和 Windows 防火墙上的 SQL 流量。 

## <a name="1---create-the-resource-group"></a>1 - 创建资源组
使用以下 PowerShell 代码片段创建新的资源组：

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - 创建两个托管实例
使用“[Azure 门户](https://portal.azure.com)”在此新资源组中创建两个托管实例。 

- 发布服务器托管实例的名称应为：`sql-mi-publisher`（带有用于随机化的几个字符），虚拟网络的名称应该为 `vnet-sql-mi-publisher`。
- 分发服务器托管实例的名称应为：`sql-mi-distributor`（带有用于随机化的几个字符），并且应在发布服务器托管实例所在的虚拟网络中  。

   ![为分发服务器使用发布服务器 vnet](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

有关创建托管实例的详细信息，请参阅[在门户中创建托管实例](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > 为简单起见，因为这是最常见的配置，所以本教程建议将分发服务器托管实例放置在发布服务器所在的虚拟网络中。 但是，可以在单独的虚拟网络中创建分发服务器。 为此，你将需要在发布服务器和分发服务器的虚拟网络之间配置 VPN 对等互连，然后在分发服务器和订阅服务器的虚拟网络之间配置 VPN 对等互连。 

## <a name="3---create-a-sql-server-vm"></a>3 - 创建 SQL Server VM
使用 [Azure 门户](https://portal.azure.com)创建 SQL Server 虚拟机。 SQL Server 虚拟机应具有以下特征：

- 名称：`sql-vm-sub`
- 映像：SQL Server 2016 或更高版本
- 资源组：与托管实例相同
- 虚拟网络：`sql-vm-sub-vnet` 

有关将 SQL Server VM 部署到 Azure 的详细信息，请参阅 [快速入门：创建 SQL Server VM](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)。

## <a name="4---configure-vpn-peering"></a>4 - 配置 VPN 对等互连
配置 VPN 对等互连，以便在两个托管实例的虚拟网络和 SQL Server 的虚拟网络之间启用通信。 为此，请使用以下 PowerShell 代码片段：

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

建立 VPN 对等互连后，请测试连接，方法是：启动 SQL Server 上的 SQL Server Management Studio (SSMS) 并连接到这两个托管实例。 有关使用 SSMS 连接到托管实例的详细信息，请参阅[使用 SSMS 连接到 MI](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)。 

![测试与托管实例的连接](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - 创建专用 DNS 区域

专用 DNS 区域允许在托管实例和 SQL Server 之间进行 DNS 路由。 

### <a name="create-private-dns-zone"></a>创建专用 DNS 区域
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“+ 创建资源”，创建新的 Azure 资源  。 
1. 在 Azure 市场中搜索 `private dns zone`。 
1. 选择 Microsoft 发布的“专用 DNS 区域”  资源，然后选择“创建”  以创建 DNS 区域。 
1. 从下拉项中选择订阅和资源组。 
1. 为 DNS 区域提供任意名称，如 `repldns.com`。 

   ![创建专用 DNS 区域](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. 选择“查看 + 创建”  。 查看专用 DNS 区域的参数，然后选择“创建”  以创建资源。 

### <a name="create-a-record"></a>创建记录

1. 转到新的“专用 DNS 区域”  并选择“概述”  。 
1. 选择“+ 记录集”  以创建新的记录。 
1. 提供 SQL Server VM 的名称以及专用内部 IP 地址。 

   ![配置记录](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. 选择“确定”  以创建记录。 

### <a name="link-the-virtual-network"></a>链接虚拟网络

1. 转到“专用 DNS 区域”并选择“虚拟网络链接”   。 
1. 选择“+ 添加”  。 
1. 提供链接的名称，如 `Pub-link`。 
1. 从下拉列表中选择订阅，然后选择发布服务器托管实例的虚拟网络。 
1. 选中“启用自动注册”旁边的框  。 

   ![创建 vnet 链接](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. 选择“确定”以链接虚拟网络  。 
1. 重复这些步骤，为订阅服务器虚拟网络添加一个链接，并对其命名，例如 `Sub-link`。 


## <a name="6---create-azure-storage-account"></a>6 - 创建 Azure 存储帐户

为工作目录[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，并在存储帐户中创建[文件共享](../storage/files/storage-how-to-create-file-share.md)。 

按以下格式复制文件共享路径：`\\storage-account-name.file.core.windows.net\file-share-name`   

示例： `\\replstorage.file.core.windows.net\replshare`

按以下格式复制存储访问密钥连接字符串：`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

示例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


有关详细信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。 


## <a name="7---create-a-database"></a>7 - 创建数据库
在发布服务器 MI 上创建新数据库。 为此，请执行下列步骤：

1. 在 SQL Server 上启动 SQL Server Management Studio (SSMS)。 
1. 连接到 `sql-mi-publisher` 托管实例。 
1. 打开“新建查询”窗口，并执行以下 T-SQL 查询以创建数据库  ：

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT *FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - 配置分发 
建立连接并具有示例数据库后，可以在 `sql-mi-distributor` 托管实例上配置分发。 为此，请执行下列步骤：

1. 在 SQL Server 上启动 SQL Server Management Studio (SSMS)。 
1. 连接到 `sql-mi-distributor` 托管实例。 
1. 打开“新建查询”窗口，并运行以下 Transact-SQL 代码，在分发服务器托管实例上配置分发  ： 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > 请确保对 @working_directory 参数仅使用反斜杠（`\`）。 在连接到文件共享时使用正斜杠（`/`）可能会导致错误。 

1. 连接到 `sql-mi-publisher` 托管实例。 
1. 打开“新建查询”窗口，并运行以下 Transact-SQL 代码，从而在发布服务器上注册分发服务器  ： 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - 创建发布
配置分发后，现在可以创建发布。 为此，请执行下列步骤： 

1. 在 SQL Server 上启动 SQL Server Management Studio (SSMS)。 
1. 连接到 `sql-mi-publisher` 托管实例。 
1. 在“对象资源管理器”中，展开“复制”节点，然后右键单击“本地发布”文件夹    。 单击“新建发布...”  。 
1. 选择“下一步”，离开“欢迎”页  。 
1. 在“发布数据库”页上，选择之前创建的 `ReplTutorial` 数据库  。 选择“**下一页**”。 
1. 在“发布类型”页上，选择“事务发布”   。 选择“**下一页**”。 
1. 在“项目”页上，选中“表”旁边的框   。 选择“**下一页**”。 
1. 在“筛选器表行”页上，选择“下一步”而不添加任何筛选器   。 
1. 在“快照代理”页上，选中“立即创建快照并使快照保持可用状态，以初始化订阅”旁边的框   。 选择“**下一页**”。 
1. 在“代理安全性”页上，选择“安全设置..”   。提供要用于快照代理的 SQL Server 登录凭据，并连接到发布服务器。 选择“确定”以关闭“快照代理安全性”页   。 选择“**下一页**”。 

   ![配置快照代理安全性](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. 在“向导操作”页上，选择“创建发布”并（视情况）选择“生成包含创建发布的步骤的脚本文件”（如果要保存此脚本以供以后使用）    。 
1. 在“完成向导”页上，将发布命名为 `ReplTest` 并选择“下一步”以创建发布   。 
1. 创建发布后，请刷新“对象资源管理器”中的“复制”节点，并展开“本地发布”查看新发布    。 


## <a name="10---create-the-subscription"></a>10 - 创建订阅 

创建发布后，可以创建订阅。 为此，请执行下列步骤： 

1. 在 SQL Server 上启动 SQL Server Management Studio (SSMS)。 
1. 连接到 `sql-mi-publisher` 托管实例。 
1. 打开“新建查询”窗口，并运行以下 Transact-SQL 代码，以添加订阅和分发代理  。 使用 DNS 作为订阅服务器名称的一部分。 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - 测试复制 

配置复制后，可对其进行测试，方法是：在发布服务器上插入新项并监视更改传播到订阅服务器。 

运行以下 T-SQL 代码片段可查看订阅服务器上的行：

```sql
Use ReplSub
select * from dbo.ReplTest
```

运行以下 T-SQL 代码片段可在发布服务器上插入更多行，然后再次检查订阅服务器上的行。 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清理资源

1. 在 [Azure 门户](https://portal.azure.com)中导航到资源组。 
1. 选择托管实例，然后选择“删除”  。 在文本框中键入 `yes` 以确认要删除该资源，然后选择“删除”  。 此过程可能需要一段时间才能在后台完成，在完成之前，你将无法删除“虚拟群集”或任何其他从属资源  。 监视“活动”选项卡中的“删除”，确认已删除托管实例。 
1. 删除托管实例后，请将虚拟群集删除，方法是：在资源组中选择“虚拟群集”，然后选择“删除”   。 在文本框中键入 `yes` 以确认要删除该资源，然后选择“删除”  。 
1. 删除所有剩余资源。 在文本框中键入 `yes` 以确认要删除该资源，然后选择“删除”  。 
1. 删除资源组，方法是：选择“删除资源组”，键入资源组的名称 `myResourceGroup`，然后选择“删除”   。 

## <a name="known-errors"></a>已知错误

### <a name="windows-logins-are-not-supported"></a>不支持 Windows 登录名

`Exception Message: Windows logins are not supported in this version of SQL Server.`

代理是使用 Windows 登录名配置的，需要改用 SQL Server 登录名。 使用“发布属性”的“代理安全性”页，将登录凭据更改为 SQL Server 登录名   。 


### <a name="failed-to-connect-to-azure-storage"></a>未能连接到 Azure 存储


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Obtained Azure Storage Connection String for replstorage 2019-11-19 02:21:05.07 Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Failed to connect to Azure Storage '' with OS error:53.


这很可能是因为在 Azure 防火墙和/或 Windows 防火墙中关闭了端口 445。 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

在文件共享的文件路径中使用正斜杠而不是反斜杠会导致此错误。 这没关系：`\\replstorage.file.core.windows.net\replshare` 这可能会导致 OS 55 错误：`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>无法连接到订阅服务器

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

可能的解决方法：
- 确保端口 1433 处于打开状态。 
- 确保在订阅服务器上启用 TCP/IP。 
- 确认在创建订阅服务器时使用了 DNS 名称。 
- 验证虚拟网络是否已正确链接到专用 DNS 区域。 
- 验证是否已正确配置 A 记录。 
- 验证是否正确配置了 VPN 对等互连。 

### <a name="no-publications-to-which-you-can-subscribe"></a>没有可以订阅的发布

使用“新建订阅”向导添加新订阅时，你可能会发现没有作为可用选项列出的数据库和发布，并且可能会看到以下错误消息：  

`There are no publications to which yuo can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
虽然此错误消息可能是准确的，但实际上连接到的发布服务器上没有可用的发布，或者你缺少足够权限，此错误也可能是由较旧版本的 SQL Server Management Studio 所致。 尝试升级到 SQL Server Management Studio 18.0 或更高版本，以排除这是根本原因。 


## <a name="next-steps"></a>后续步骤

### <a name="enable-security-features"></a>启用安全功能

请参阅[托管实例安全功能](sql-database-managed-instance.md#azure-sql-database-security-features)一文，全面了解保护数据库的各种方式。 此文介绍了以下安全功能：

- [托管实例审核](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [威胁检测](sql-database-managed-instance-threat-detection.md) 
- [动态数据屏蔽](/sql/relational-databases/security/dynamic-data-masking)
- [行级安全](/sql/relational-databases/security/row-level-security) 
- [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>托管实例功能

有关托管实例功能的完整概述，请参阅：

> [!div class="nextstepaction"]
> [托管实例功能](sql-database-managed-instance.md)
