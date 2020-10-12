---
title: 配置到 Azure SQL Edge 的复制
description: 了解如何配置到 Azure SQL Edge 的复制。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a748bf977e76357c710518e608c12ad19a8cd0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888427"
---
# <a name="configure-replication-to-azure-sql-edge"></a>配置到 Azure SQL Edge 的复制 

可以将 Azure SQL Edge 实例配置为单向事务复制或快照复制的推送订阅服务器。 此实例不能充当事务复制配置的发布服务器或分发服务器。 请注意，Azure SQL Edge 不支持合并复制、对等复制或 Oracle 发布。

## <a name="supported-configurations"></a>支持的配置
  
- Azure SQL Edge 实例必须是发布服务器的推送订阅服务器。
- 发布服务器和分发服务器可以是：
   - 在本地运行的 SQL Server 实例，也可以是在 Azure 虚拟机中运行的 SQL Server 实例。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](https://docs.microsoft.com/azure/azure-sql/virtual-machines/)。 SQL Server 实例必须使用高于 SQL Server 2016 的版本。
   - Azure SQL 托管实例的实例。 SQL 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关详细信息，请参阅[通过 SQL 数据库托管实例进行复制](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)。

- 不能将分发数据库和复制代理置于一个 Azure SQL Edge 实例上。  

> [!NOTE]
> 如果尝试使用不受支持的版本配置复制，可能会收到以下两个错误：MSSQL_REPL20084（“进程无法连接到订阅服务器。”）和 MSSQL_REPL40532（“无法打开登录请求的服务器 \<name>。 登录失败。”）  

## <a name="remarks"></a>备注

在配置复制时，必须了解以下要求和最佳做法：

- 可以使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 配置复制。 还可以通过使用 SQL Server Management Studio 或 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) 在发布服务器上运行 Transact-SQL 语句来执行此操作。
- 若要复制到 Azure SQL Edge 实例，必须使用 SQL Server 身份验证登录。
- 复制的表必须有主键。
- SQL Server 上的单一发布可以支持 Azure SQL Edge 和 SQL Server（本地 SQL Server 以及 Azure 虚拟机中的 SQL Server）订阅服务器。  
- 必须从 SQL Server 实例执行复制管理、监视和故障排除。  
- 仅支持推送 Azure SQL Edge 订阅。  
- Azure SQL Edge 的存储过程 `sp_addsubscription` 中仅支持 `@subscriber_type = 0`。  
- Azure SQL Edge 不支持双向、即时、可更新或对等复制。
- Azure SQL Edge 仅支持 SQL Server 或 SQL 托管实例中可用的功能子集。 如果尝试复制包含一个或多个不支持的功能的数据库（或数据库中的对象），尝试将失败。 例如，如果尝试复制包含空间数据类型对象的数据库，则会收到错误。 有关详细信息，请参阅 [Azure SQL Edge 支持的功能](features.md)。

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>在 Azure SQL Edge 实例上初始化参考数据

你可能需要使用随时间变化的参考数据初始化实例。 例如，在 SQL Server 实例上对机器学习模型进行训练后，你可能需要更新 Azure SQL Edge 实例上的这些模型。 下面是在这种情况下初始化实例的方法：

1. 在 SQL Server 数据库上创建事务复制发布。  
2. 在 SQL Server 实例上使用“新建订阅向导”或 Transact-SQL 语句创建 Azure SQL Edge 订阅推送。  
3. 可以使用快照代理生成并由分发代理分发和交付的快照来初始化 Azure SQL Edge 上的复制数据库。 或者，可以使用发布服务器上的数据库备份来进行初始化。 请记住，如果数据库备份包含 Azure SQL Edge 不支持的对象或功能，还原操作将失败。

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
- 复制文件流、`hierarchyid` 或空间数据类型
- 将 `hierarchyid` 转换为 MAX 数据类型  
- 将空间转换为 MAX 数据类型  
- 复制扩展的属性  
- 复制权限  

## <a name="examples"></a>示例

创建发布和推送订阅。 有关详细信息，请参阅：
  
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 使用 Azure SQL Edge 服务器名称和 IP 作为订阅服务器（例如 myEdgeinstance,1433），并使用 Azure SQL Edge 实例上的数据库名称作为目标数据库（例如 AdventureWorks）来[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) 。  

## <a name="next-steps"></a>后续步骤  

- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


