---
title: 配置到 Azure SQL Edge（预览版）的复制
description: 了解如何配置 Azure SQL Edge （预览版）的复制。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a33933e63cc7c15de7d60430521f810f8546988c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667873"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>配置到 Azure SQL Edge（预览版）的复制 

可以将 Azure SQL Edge 的实例配置为推送订阅服务器，以便进行单向事务复制或快照复制。 此实例不能充当事务复制配置的发布服务器或分发服务器。 请注意，Azure SQL Edge 不支持合并复制、对等复制或 Oracle 发布。

## <a name="supported-configurations"></a>支持的配置
  
- Azure SQL Edge 的实例必须是发布服务器的推送订阅服务器。
- 发布服务器和分发服务器可以是：
   - SQL Server 在本地运行的实例，或在 Azure 虚拟机中运行 SQL Server 的实例。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)。 SQL Server 实例必须使用比 SQL Server 2016 更高的版本。
   - Azure SQL 托管实例的实例。 SQL 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关详细信息，请参阅[通过 SQL 数据库托管实例进行复制](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)。

- 不能将分发数据库和复制代理放置在 Azure SQL Edge 的实例上。  

> [!NOTE]
> 如果尝试使用不受支持的版本配置复制，可能会收到以下两个错误： MSSQL_REPL20084 （"进程无法连接到订阅服务器。"）和 MSSQL_REPL40532 （"无法打开 \<name> 登录所请求的服务器。 登录失败。 "）。  

## <a name="remarks"></a>注解

在配置复制时，必须了解以下要求和最佳做法：

- 您可以使用[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)配置复制。 还可以通过在发布服务器上运行 Transact-sql 语句来执行此操作，方法是使用 SQL Server Management Studio 或[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)。
- 若要复制到 Azure SQL Edge 的实例，必须使用 SQL Server 身份验证登录。
- 复制的表必须有主键。
- SQL Server 上的单一发布可以支持 Azure SQL Edge 和 SQL Server（本地 SQL Server 以及 Azure 虚拟机中的 SQL Server）订阅服务器。  
- 必须从 SQL Server 实例执行复制管理、监视和故障排除。  
- 仅支持推送 Azure SQL Edge 订阅。  
- `@subscriber_type = 0`AZURE SQL Edge 的存储过程仅支持 `sp_addsubscription` 。  
- Azure SQL Edge 不支持双向、即时、可更新或对等复制。
- Azure SQL Edge 仅支持 SQL Server 或 SQL 托管实例中可用功能的子集。 如果尝试复制包含一个或多个不支持的功能的数据库（或数据库中的对象），则尝试将失败。 例如，如果尝试复制的数据库包含具有空间数据类型的对象，则会收到错误。 有关详细信息，请参阅[AZURE SQL Edge 支持的功能](features.md)。

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>在 Azure SQL Edge 的实例上初始化引用数据

您可能想要用随时间变化的引用数据初始化实例。 例如，你可能想要在 Azure SQL Edge 的实例上更新机器学习模型，并在 SQL Server 实例上训练它们。 下面是在这种情况下初始化实例的方法：

1. 在 SQL Server 数据库上创建事务复制发布。  
2. 在 SQL Server 实例上，使用**新建订阅向导**或 transact-sql 语句来创建对 Azure SQL Edge 的订阅的推送。  
3. 你可以使用快照代理生成的快照在 Azure SQL Edge 上初始化复制的数据库，并由分发代理分发和传递。 或者，您可以通过使用发布服务器上的数据库备份来进行初始化。 请记住，如果数据库备份包含 Azure SQL Edge 不支持的对象或功能，则还原操作将失败。

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
- 复制 filestream、 `hierarchyid` 或空间数据类型
- 转换 `hierarchyid` 为 MAX 数据类型  
- 将空间转换为 MAX 数据类型  
- 复制扩展的属性  
- 复制权限  

## <a name="examples"></a>示例

创建发布和推送订阅。 有关详细信息，请参阅：
  
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 使用 Azure SQL Edge 服务器名称和 IP 作为订阅服务器（例如， **myEdgeinstance，1433**），并将 Azure sql edge 实例上的数据库名称用作目标数据库（例如**AdventureWorks**）来[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)。  

## <a name="next-steps"></a>后续步骤  

- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


