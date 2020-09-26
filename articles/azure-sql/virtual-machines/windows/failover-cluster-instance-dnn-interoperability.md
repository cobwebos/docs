---
title: 功能与 SQL Server FCI & DNN 的互操作性
description: '了解在使用 Azure Vm SQL Server 上的故障转移群集实例时，使用某些 SQL Server 功能和分布式网络名称 (DNN) 资源时的其他注意事项。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: ca782e9949f990857db408919cac342d7f712d2b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272610"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>功能与 SQL Server FCI & DNN 的互操作性
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

某些 SQL Server 功能依赖于硬编码的虚拟网络名称 (VNN) 。 因此，使用分布式网络名称 (DNN) 资源与故障转移群集实例和 Azure Vm SQL Server，还需要考虑一些其他注意事项。 

本文介绍如何在使用 DNN 资源时配置网络别名，以及哪些 SQL Server 功能需要额外的注意事项。

## <a name="create-network-alias-fci"></a> (FCI 创建网络别名) 

某些服务器端组件依赖于硬编码的 VNN 值，需要将 VNN 映射到 DNN DNS 名称才能正常运行的网络别名。 按照 [创建服务器别名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) 中的步骤创建一个别名，以将 VNN 映射到 DNN DNS 名称。 

对于默认实例，可以直接将 VNN 映射到 DNN DNS 名称，以便 VNN = DNN DNS 名称。
例如，如果 VNN name 为 `FCI1` ，则 instance name 为 `MSSQLSERVER` ，DNN `FCI1DNN` (以前连接到的客户端 `FCI` ，现在它们连接到 `FCI1DNN`) 然后将 VNN 映射 `FCI1` 到 DNN `FCI1DNN` 。 

对于命名实例，应为完整实例完成网络别名映射，例如 `VNN\Instance`  =  `DNN\Instance` 。 例如，如果 VNN name 为 `FCI1` ，则 instance name 为 `instA` ，DNN `FCI1DNN` (以前连接到的客户端 `FCI1\instA` ，现在它们连接到 `FCI1DNN\instaA`) 然后将 VNN 映射 `FCI1\instaA` 到 DNN `FCI1DNN\instaA` 。 



## <a name="client-drivers"></a>客户端驱动程序

对于 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驱动程序，用户需要在连接字符串中显式指定 DNN DNS 名称作为服务器名称。 若要确保在故障转移时快速连接，请在 `MultiSubnetFailover=True` SQL 客户端支持连接字符串时将其添加到连接字符串中。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、 [sqlcmd](/sql/tools/sqlcmd-utility)、 [Azure Data Studio](/sql/azure-data-studio/what-is)和[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools)的用户需要在连接字符串中显式指定 DNN DNS 名称作为服务器名称。 

## <a name="availability-groups-and-fci"></a>可用性组和 FCI

可以通过将故障转移群集实例用作副本之一来配置 Always On 可用性组。 在此配置中，FCI 副本的镜像终结点 URL 需要使用 FCI DNN。 同样，如果将 FCI 用作只读副本，则 FCI 副本的只读路由需要使用 FCI DNN。 

镜像终结点的格式为： `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` 。 

例如，如果 DNN DNS 名称是 `dnnlsnr` ，并且 `5022` 是 FCI 的镜像终结点的端口，则用于创建终结点 URL 的 Transact-sql (t-sql) 代码段如下所示： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同样，只读路由 URL 的格式为： `TCP://<DNN DNS name>:<SQL Server instance port>` 。 

例如，如果 DNN DNS 名称是 `dnnlsnr` ，并且 `1444` 是只读目标 SQL Server FCI 使用的端口，则用于创建只读路由 URL 的 t-sql 代码段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

如果 URL 是默认的1433端口，则可以省略该端口。 对于命名实例，为命名实例配置静态端口，并在只读路由 URL 中指定该端口。  

## <a name="replication"></a>复制

复制具有三个组件：发布服务器、分发服务器、订阅服务器。 这些组件中的任何一个都可以是故障转移群集实例。 由于 FCI VNN 在复制配置中广泛使用，这两个都是显式和隐式的，因此，可能需要将 VNN 映射到 DNN，以便复制能够正常工作。 

继续在复制中使用 VNN 名称作为 FCI 名称，但在 *配置复制之前*在以下远程情况下创建网络别名：

| **复制组件 (FCI 与 DNN) ** | **远程组件** | **网络别名映射** | **带有网络映射的服务器**| 
|---------|---------|---------|-------- | 
|发布者 | 分发服务器 | 发布服务器 VNN 到发布服务器 DNN| 分发服务器| 
|分发服务器|订阅者 |分发服务器到分发服务器的 VNN DNN| 订阅者 | 
|分发服务器|发布者 | 分发服务器到分发服务器的 VNN DNN | 发布者| 
|订阅者| 分发服务器| 订阅服务器 VNN 订阅服务器 DNN | 分发服务器| 

例如，假设你有一个在复制拓扑中使用 DNN 配置为 FCI 的发布服务器，而该分发服务器是远程的。 在这种情况下，请在分发服务器上创建一个网络别名，以将发布服务器 VNN 映射到发布服务器 DNN： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="使用 SQL Server 配置管理器将 DNN DNS 名称配置为网络别名。" :::

使用命名实例的完整实例名称，如下图所示： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="为命名实例配置网络别名时，请使用完整的实例名称。" :::

## <a name="database-mirroring"></a>数据库镜像

可以使用 FCI 作为数据库镜像伙伴来配置数据库镜像。 使用 [transact-sql (t-sql) ](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) 而不是 SQL Server Management Studio GUI 来配置它。 使用 T-sql 将确保使用 DNN 而不是 VNN 创建数据库镜像端点。 

例如，如果 DNN DNS 名称为 `dnnlsnr` ，并且数据库镜像端点为7022，则以下 t-sql 代码段将配置数据库镜像伙伴： 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

对于客户端访问， **故障转移伙伴** 属性可以处理数据库镜像故障转移，但不能处理 FCI 故障转移。 

## <a name="msdtc"></a>MSDTC

FCI 可以参与由 Microsoft 分布式事务处理协调器 (MSDTC) 协调的分布式事务。 尽管群集 MSDTC 和本地 MSDTC 都支持 FCI DNN，但在 Azure 中，群集 MSDTC 仍需要负载均衡器。 在 FCI 中定义的 DNN 不会替换 Azure 中群集 MSDTC 的 Azure 负载均衡器要求。 

## <a name="filestream"></a>FileStream

尽管 FileStream 支持 FCI 中的数据库，但不支持通过将文件系统 Api 与 DNN 配合使用来访问 FileStream 或 FileTable。 

## <a name="linked-servers"></a>链接服务器

支持在 FCI DNN 中使用链接服务器。 直接使用 DNN 配置链接服务器，或使用网络别名将 VNN 映射到 DNN。 


例如，若要使用命名实例的 DNN DNS 名称创建链接服务器 `dnnlsnr` `insta1` ，请使用以下 (transact-sql) 命令：

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

或者，你可以使用 (VNN) 的虚拟网络名称创建链接服务器，但你需要定义一个网络别名，以将 VNN 映射到 DNN。 

例如，对于 "名称 `insta1` "、"VNN" `vnnname` 和 "DNN 名称" `dnnlsnr` ，请使用以下 transact-sql (t-sql) 命令，使用 VNN 创建链接服务器：

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

然后，创建要映射到的网络 `vnnname\insta1` 别名 `dnnlsnr\insta1` 。 



## <a name="frequently-asked-questions"></a>常见问题


- 哪个 SQL Server 版本引入了 DNN 支持？ 

   SQL Server 2019 CU2 及更高版本。

- 使用 DNN 时的预期故障转移时间是多少？

   对于 DNN，故障转移时间只是 FCI 故障转移时间，在使用 Azure 负载均衡器) 时，不会 (如探测时间添加任何时间。

- SQL 客户端是否有任何版本要求支持 OLEDB 和 ODBC DNN？

   建议 `MultiSubnetFailover=True` 为 DNN 提供连接字符串支持。 从 SQL Server 2012 (11. x) 开始提供此功能。

- 使用 DNN 需要 SQL Server 配置更改吗？ 

   SQL Server 不需要更改任何配置即可使用 DNN，但某些 SQL Server 功能可能需要更多的注意事项。 

- DNN 是否支持多子网群集？

   是的。 群集将 DNS 中的 DNN 绑定到群集中所有节点的物理 IP 地址，而不考虑子网。 SQL 客户端尝试 DNS 名称的所有 IP 地址，而不考虑子网。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

