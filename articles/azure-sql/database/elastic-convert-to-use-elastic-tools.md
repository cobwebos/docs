---
title: 迁移要扩展的现有数据库
description: 通过创建分片映射管理器来转换分片数据库，以使用弹性数据库工具
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 95afd0b9117caca4b531c453417217d8a8de7c7f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443478"
---
# <a name="migrate-existing-databases-to-scale-out"></a>迁移要扩展的现有数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

使用工具（例如[弹性数据库客户端库](elastic-database-client-library.md)）轻松管理现有的横向扩展共享数据库。 先转换现有数据库集，再使用[分片映射管理器](elastic-scale-shard-map-management.md)。

## <a name="overview"></a>概述

迁移现有的分片数据库：

1. 准备[分片映射管理器数据库](elastic-scale-shard-map-management.md)。
2. 创建分片映射。
3. 准备各个分片。  
4. 将映射添加到分片映射。

可使用 [.NET Framework 客户端库](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)或者 [Azure SQL 数据库 - 弹性数据库工具脚本](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)中提供的 PowerShell 脚本来实现这些技巧。 以下示例使用 PowerShell 脚本。

有关 ShardMapManager 的详细信息，请参阅[分片映射管理](elastic-scale-shard-map-management.md)。 有关弹性数据库工具的概述，请参阅[弹性数据库功能概述](elastic-scale-introduction.md)。

## <a name="prepare-the-shard-map-manager-database"></a>准备分片映射管理器数据库

分片映射管理器是一个特殊的数据库，其中包含用于管理已扩展数据库的数据。 可以使用现有数据库，或创建新的数据库。 用作分片映射管理器的数据库不应是与分片相同的数据库。 PowerShell 脚本不会创建该数据库。

## <a name="step-1-create-a-shard-map-manager"></a>步骤 1：创建分片映射管理器

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>检索分片映射管理器

创建后，可以使用此 cmdlet 检索分片映射管理器。 每当需要使用 ShardMapManager 对象时，则需要执行此步骤。

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>步骤 2：创建分片映射

必须选择要创建的分片映射类型。 选择取决于数据库架构：

1. 每个数据库一个租户（有关术语，请参阅[词汇表](elastic-scale-glossary.md)。）
2. 每个数据库多个租户（两种类型）：
   1. 列表映射
   2. 范围映射

对于单租户模型，创建“列表映射”  分片映射。 单租户模型将每个租户分配给一个数据库。 这是适用于 SaaS 开发人员的有效模型，因为它可以简化管理。

![列表映射][1]

多租户模型将数个租户分配给单个数据库（可跨多个数据库分布租户组）。 如果希望每个租户具有较小的数据需求，请使用此模型。 在此模型中，使用范围映射将一系列用户分配到数据库。

![范围映射][2]

或可以使用列表映射来实现多租户数据库模型，以将多个租户分配给单个数据库。 例如，DB1 用于存储租户 ID 1 和 5 的相关信息，而 DB2 用于存储租户 7 和租户 10 的数据。

![单一数据库上的多个租户][3]

**根据你具体的选择，选择以下选项之一：**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>选项 1：为列表映射创建分片映射

使用 ShardMapManager 对象创建分片映射。

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>选项 2：为范围映射创建分片映射

若要使用此映射模式，租户 ID 值需是连续范围，并且可接受范围中有间距，方法为创建数据库时跳过范围。

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>选项 3：单个数据库的列表映射

设置此模式也要求创建列表映射，如步骤 2，选项 1 中所示。

## <a name="step-3-prepare-individual-shards"></a>步骤 3：准备各个分片

将每个分片（数据库）添加到分片映射管理器。 此操作将准备用于存储映射信息的各个数据库。 对每个分片执行此方法。

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>步骤 4：添加映射

添加映射的操作取决于所创建的分片映射种类。 如果创建的是列表映射，则添加列表映射。 如果创建的是范围映射，则添加范围映射。

### <a name="option-1-map-the-data-for-a-list-mapping"></a>选项 1：为列表映射映射数据

通过为每个租户添加列表映射来映射数据。  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>选项 2：为范围映射映射数据

添加所有租户 ID 范围的范围映射 - 数据库关联：

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>步骤 4，选项 3：映射单个数据库上多个租户的数据

对于每个租户，运行 Add-ListMapping（选项 1）。

## <a name="checking-the-mappings"></a>检查映射

可以使用以下命令查询现有分片及其关联的映射的相关信息：  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>摘要

完成设置后，便可以开始使用弹性数据库客户端库。 还可以使用[数据依赖型路由](elastic-scale-data-dependent-routing.md)和[多分片查询](elastic-scale-multishard-querying.md)。

## <a name="next-steps"></a>后续步骤

从 [Azure SQL 数据库 - 弹性数据库工具脚本](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)获取 PowerShell 脚本。

GitHub 上也提供了这些工具：[Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools)。

使用拆分/合并工具在多租户模型与单租户模型之间来回移动数据。 请参阅[拆分合并工具](elastic-scale-get-started.md)。

## <a name="additional-resources"></a>其他资源

有关多租户软件即服务 (SaaS) 数据库应用程序的常见数据体系结构模式的信息，请参阅 [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。

## <a name="questions-and-feature-requests"></a>问题和功能请求

如有问题，请使用 [适用于 Sql 数据库的 Microsoft Q&问题页面](https://docs.microsoft.com/answers/topics/azure-sql-database.html) 和功能请求，并将其添加到 [sql 数据库反馈论坛](https://feedback.azure.com/forums/217321-sql-database/)。

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png
