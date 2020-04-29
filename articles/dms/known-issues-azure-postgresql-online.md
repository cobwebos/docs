---
title: 已知问题：从 PostgreSQL 联机迁移到 Azure Database for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务从 PostgreSQL 到 Azure Database for PostgreSQL 的联机迁移的已知问题和迁移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235269"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>从 PostgreSQL 联机迁移到 Azure DB for PostgreSQL 时的已知问题/迁移限制

以下部分描述了在从 PostgreSQL 联机迁移到 Azure Database for PostgreSQL 时存在的已知问题和限制。

## <a name="online-migration-configuration"></a>联机迁移配置

- 源 PostgreSQL 服务器必须运行版本 9.4、9.5、9.6、10 或 11。 有关详细信息，请参阅[支持的 PostgreSQL 数据库版本](../postgresql/concepts-supported-versions.md)一文。
- 仅支持迁移到同一版本或更高版本。 例如，支持将 PostgreSQL 9.5 迁移到 Azure Database for PostgreSQL 9.6 或 10，但不支持从 PostgreSQL 11 迁移到 PostgreSQL 9.6。
- 若要在源 PostgreSQL postgresql.config 文件中启用逻辑复制，请设置以下参数  ：
  - wal_level = logical 
  - **max_replication_slots** = [要迁移的数据库最大数目的下限]；如果要迁移四个数据库，请将该值设置为至少为 4。
  - max_wal_senders = [并发运行的数据库数]；建议值为 10 
- 向源 PostgreSQL pg_hba.conf 添加 DMS 代理 IP
  1. 预配 Azure 数据库迁移服务后，记下 DMS IP 地址。
  2. 向 pg_hba.conf 文件添加 IP 地址，如下所示：

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- 用户必须在托管源数据库的服务器上具有“复制”角色。
- 源数据库架构和目标数据库架构必须匹配。
- 目标 Azure Database for PostgreSQL 单一服务器中的架构不能包含外键。 使用以下查询来删除外键：

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    运行查询结果中的 drop foreign key（第二列）。

- 目标 Azure Database for PostgreSQL 单一服务器中的架构不能包含任何触发器。 若要禁用目标数据库中的触发器，请使用以下设置：

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>数据类型限制

  **限制**：如果表中没有主键，则所做的更改可能不会同步到目标数据库。

  **解决方法**：暂时为表设置一个主键，以便迁移能够继续。 数据迁移完成后，可以删除该主键。

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>从 AWS RDS PostgreSQL 联机迁移时的限制

尝试从 AWS RDS PostgreSQL 联机迁移到 Azure Database for PostgreSQL 时，你可能会遇到以下错误。

- **错误**：数据库“{database}”的表“{table}”中的列“{column}”的默认值在源服务器和目标服务器上有所不同。 在源服务器上，值为“{value on source}”，而在目标服务器上，值则为“{value on target}”。

  **限制**：如果列架构上的默认值在源数据库与目标数据库之间有所不同，则会出现此错误。
  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **错误**：目标数据库“{database}”包含“{number of tables}”个表，而源数据库“{database}”包含“{number of tables}”个表。 源数据库和目标数据库中表的数目应当匹配。

  **限制**：当源数据库与目标数据库的表数不同时，将出现此错误。

  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **错误：** 源数据库 {database} 为空

  **限制**：当源数据库为空时，会出现此错误。 这最有可能是因为你选择了错误的数据库作为源数据库。

  **解决方法**：反复检查选择迁移的源数据库，然后重试。

- **错误：** 目标数据库 {database} 为空。 请迁移架构。

  **限制**：当目标数据库上没有架构时，会出现此错误。 确保目标上的架构与源上的架构匹配。
  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

## <a name="other-limitations"></a>其他限制

- 数据库名称不能包含分号 (;)。
- 已捕获的表必须包含主键。 如果某个表没有主键，则删除和更新记录操作的结果将不可预测。
- 忽略更新主键段。 在这种情况下，应用此类更新将被目标标识为未更新任何行的更新，并且将导致向异常表写入记录。
- 迁移名称相同但包含不同案例（例如，table1、TABLE1 和 Table1）的多个表可能导致不可预测的行为，因此不支持。
- 不支持更改 [CREATE | ALTER | DROP | TRUNCATE] table DDL 的处理。
- 在 Azure 数据库迁移服务中，单个迁移活动最多只能容纳四个数据库。
