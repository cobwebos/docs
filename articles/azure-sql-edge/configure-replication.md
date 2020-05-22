---
title: 配置到 Azure SQL Edge（预览版）的复制
description: 了解如何配置到 Azure SQL Edge（预览版）的复制
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593986"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>配置到 Azure SQL Edge（预览版）的复制 

可以将 Azure SQL Edge 实例配置为单向事务复制或快照复制的推送订阅服务器。 Azure SQL Edge 实例不能充当事务复制配置的发布服务器或分发服务器。 Azure SQL Edge 不支持合并复制、P2P 复制、Oracle 发布。

## <a name="supported-configurations"></a>支持的配置：
  
- Azure SQL Edge 实例必须是发布服务器的推送订阅服务器。
- 发布服务器和分发服务器可以是
   - 在本地运行的 SQL Server 实例，也可以是在 Azure 虚拟机中运行的 SQL Server 实例。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)。 SQL Server 实例必须使用高于 SQL Server 2016 的版本。
   - Azure SQL 数据库托管实例。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关详细信息，请参阅[通过 SQL 数据库托管实例进行复制](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)。

- 不能将分发数据库和复制代理置于一个 Azure SQL Edge 实例上。  

> [!NOTE]
> 尝试使用不支持的版本来配置复制可能导致错误 MSSQL_REPL20084（过程无法连接到订阅服务器）和 MSSQL_REPL40532（无法打开登录名所请求的服务器 \<name>。 登录失败）。  

若要使用 Azure SQL Edge 的所有功能，必须使用最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。  

## <a name="remarks"></a>备注

- 若要配置复制，可以使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)，也可以使用 SQL Server Management Studio 或 [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 在发布服务器上执行 Transact-SQL 语句
- 复制时，只能使用 SQL Server 身份验证登录来连接到 Azure SQL Edge 实例。
- 复制的表必须有主键。
- SQL Server 上的单一发布可以支持 Azure SQL Edge 和 SQL Server（本地 SQL Server 以及 Azure 虚拟机中的 SQL Server）订阅服务器。  
- 复制管理、监视和故障排除必须从本地 SQL Server 执行。  
- 仅支持推送 Azure SQL Edge 订阅。  
- 在适用于 Azure SQL Edge 的 sp_addsubscription 中，仅 `@subscriber_type = 0` 受支持。  
- Azure SQL Edge 不支持双向、即时、可更新或对等复制。
- Azure SQL Edge 仅支持 SQL Server 或 Azure SQL 数据库托管实例中可用的部分功能，因此试图复制包含一个或多个非支持功能的数据库（或数据库中的对象）将导致失败。 例如，尝试复制包含某些对象（具有空间数据类型）的数据库将导致错误。 有关 Azure SQL Edge 支持的功能的详细信息，请参阅[支持的 Azure SQL Edge 功能](features.md)。

## <a name="scenarios"></a>方案  

### <a name="initializing-reference-data-on-an-edge-instance"></a>在 Edge 实例上初始化参考数据

当需要使用随时间变化的参考数据来初始化 edge 实例时，复制可能会非常有用。 例如，当 ML 模型在本地 SQL Server 实例上训练后，在 Edge 实例上对其进行更新。

1. 在本地 SQL Server 数据库上创建事务复制发布。  
2. 在本地 SQL Server 上使用新建订阅向导或 Transact-SQL 语句创建一个 Azure SQL Edge 订阅推送。  
3. 若要初始化 Azure SQL Edge 上的复制数据库，可以使用快照代理生成并由分发代理分发和交付的快照，也可以使用来自发布服务器的数据库备份。 同样，如果数据库备份包含 Azure SQL Edge 不支持的对象/功能，还原操作将失败。

## <a name="limitations"></a>限制

Azure SQL Edge 订阅不支持以下选项：

- 复制文件组关联关系  
- 复制表分区方案  
- 复制索引分区方案  
- 复制用户定义的统计信息  
- 复制默认绑定  
- 复制规则绑定  
- 复制全文索引  
- 复制 XML XSD  
- 复制 XML 索引  
- 复制权限  
- 复制空间索引  
- 复制筛选的索引  
- 复制数据压缩属性  
- 复制稀疏列属性  
- 复制 filestream、hierarchyid 或空间数据类型。
- 将 hierarchyid 转换为 MAX 数据类型  
- 将空间转换为 MAX 数据类型  
- 复制扩展的属性  
- 复制权限  

## <a name="examples"></a>示例

创建发布和推送订阅。 有关详细信息，请参阅：
  
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 使用 Azure SQL Edge 服务器名称/IP 作为订阅服务器（例如 myEdgeinstance,1433），并使用 Azure SQL Edge 实例上的数据库名称作为目标数据库（例如 AdventureWorks）来[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)。  

## <a name="see-also"></a>另请参阅  

- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


