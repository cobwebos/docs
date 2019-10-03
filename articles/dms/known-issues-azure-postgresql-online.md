---
title: 有关从 PostgreSQL 到 Azure Database for PostgreSQL-单服务器的联机迁移的已知问题/迁移限制的文章 |Microsoft Docs
description: 了解从 PostgreSQL 到 Azure Database for PostgreSQL 的联机迁移的已知问题/迁移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: 891e8a261e092de0ffcef3941dd48f01942a8030
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802592"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>从 PostgreSQL 联机迁移到 Azure DB for PostgreSQL 的已知问题/迁移限制-单服务器

以下部分介绍了与从 PostgreSQL 到 Azure Database for PostgreSQL 单服务器的联机迁移相关的已知问题和限制。

## <a name="online-migration-configuration"></a>联机迁移配置

- 源 PostgreSQL 服务器必须运行版本9.5.11、9.6.7 或10.3 或更高版本。 有关详细信息，请参阅[支持的 PostgreSQL 数据库版本](../postgresql/concepts-supported-versions.md)一文。
- 仅支持相同版本的迁移。 例如，不支持将 PostgreSQL 9.5.11 迁移到 Azure Database for PostgreSQL 9.6.7。

    > [!NOTE]
    > 对于 PostgreSQL 版本 10，DMS 目前仅支持将版本 10.3 迁移到 Azure Database for PostgreSQL。 我们打算不久之后支持较新版本的 PostgreSQL。

- 若要在源 PostgreSQL postgresql.config 文件中启用逻辑复制，请设置以下参数：
  - wal_level = logical
  - **max_replication_slots** = [用于迁移的数据库的最大数量];如果要迁移四个数据库，请将值设置为4
  - max_wal_senders = [并发运行的数据库数]；建议值为 10
- 向源 PostgreSQL pg_hba.conf 添加 DMS 代理 IP
  1. 预配 DMS 实例完成以后，记下 DMS IP 地址。
  2. 向 pg_hba.conf 文件添加 IP 地址，如下所示：

        host    all     172.16.136.18/10    md5    host    replication postgres    172.16.136.18/10    md5

- 用户必须对承载源数据库的服务器具有超级用户权限
- 除了源数据库架构中具有 ENUM，源和目标数据库架构还必须匹配。
- 目标 Azure Database for PostgreSQL 中的架构不能包含外键。 使用以下查询来删除外键：

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

- Target Azure Database for PostgreSQL 中的架构不能有任何触发器。 若要禁用目标数据库中的触发器，请使用以下设置：

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>数据类型限制

- **限制**：如果源 PostgreSQL 数据库中存在 ENUM 数据类型，则在连续同步期间迁移将会失败。

    **解决方法**：将 ENUM 数据类型修改为 Azure Database for PostgreSQL 中不同的字符。

- **限制**：如果表中没有主键，连续同步将会失败。

    **解决方法**：暂时为表设置一个主键，以便迁移能够继续。 数据迁移完成后，可以删除该主键。

- **限制**：迁移不支持 JSONB 数据类型。

## <a name="lob-limitations"></a>LOB 限制

大型对象 (LOB) 列为可能增大的列。 对于 PostgreSQL，LOB 数据类型的示例包括 XML、JSON、IMAGE、TEXT 等。

- **限制**：如果将 LOB 数据类型用作主键，迁移将会失败。

    **解决方法**：将主键替换为不属于 LOB 的其他数据类型或列。

- **限制**：如果大型对象 (LOB) 列的长度超过 32 KB，目标上的数据可能会截断。 可使用以下查询检查 LOB 列的长度：

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **解决方法**：如果 LOB 对象大于 32 KB，请在[请求 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)时联系工程团队。

- **限制**：如果表中包含 LOB 列，切没有用于表的主键集，则数据可能不会为此表迁移。

    **解决方法**：暂时为表设置一个主键，以便迁移能够继续。 数据迁移完成后，可以删除该主键。

## <a name="postgresql10-workaround"></a>PostgreSQL10 解决方法

PostgreSQL 10.x 对 pg_xlog 文件夹名称进行各种更改，并因此导致迁移未按预期运行。 如果要从 PostgreSQL 10.x 迁移到 Azure Database for PostgreSQL 10.3，请对源 PostgreSQL 数据库执行以下脚本，以围绕 pg_xlog 函数创建包装程序函数。

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>从 AWS RDS PostgreSQL 联机迁移时的限制

尝试从 AWS RDS PostgreSQL 联机迁移到 Azure Database for PostgreSQL 时，你可能会遇到以下错误。

- **错误**：数据库“{database}”的表“{table}”中的列“{column}”的默认值在源服务器和目标服务器上有所不同。 在源服务器上，值为“{value on source}”，而在目标服务器上，值则为“{value on target}”。

  **限制**：如果列架构上的默认值在源数据库与目标数据库之间有所不同，则会出现此错误。
  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **错误**：目标数据库“{database}”包含“{number of tables}”个表，而源数据库“{database}”包含“{number of tables}”个表。 源数据库和目标数据库中表的数目应当匹配。

  **限制**：当源数据库与目标数据库的表数不同时，将出现此错误。
  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

- **错误：** 源数据库 {database} 为空

  **限制**：当源数据库为空时，会出现此错误。 这最有可能是因为已将错误的数据库选为源数据库。
  **解决方法**：反复检查选择迁移的源数据库，然后重试。

- **错误：** 目标数据库 {database} 为空。 请迁移架构。

  **限制**：当目标数据库上没有架构时，会出现此错误。 确保目标上的架构与源上的架构匹配。
  **解决方法**：确保目标上的架构与源上的架构匹配。 有关迁移架构的详细信息，请参阅 [Azure PostgreSQL 联机迁移文档](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)。

## <a name="other-limitations"></a>其他限制

- 数据库名称不能包含分号 (;)。
- 不支持包含左右大括号 {  } 的密码字符串。 连接到源 PostgreSQL 和目标 Azure Database for PostgreSQL 时，都存在这种限制。
- 已捕获的表必须包含主键。 如果某个表没有主键，则删除和更新记录操作的结果将不可预测。
- 忽略更新主键段。 在这种情况下，应用此类更新将被目标标识为未更新任何行的更新，并且将导致向异常表写入记录。
- 迁移名称相同但包含不同案例（例如，table1、TABLE1 和 Table1）的多个表可能导致不可预测的行为，因此不支持。
- 支持 [创建 | 更改 | 删除] 表 DDL 的更改处理，除非他们保存在内部函数/过程体或其他嵌套构造中。 例如，不会捕获以下更改：

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- 不支持 TRUNCATE 操作的更改处理（连续同步）。 不支持已分区表的迁移。 检测到已分区表时，会出现以下情况：

  - 数据库将报告父表和子表列表。
  - 将创建关于目标的表，作为具有与所选表相同属性的常规表。
  - 如果源数据库中具有与其子表相同的主键值，将产生“重复键”错误。

- 在 DMS 中，可在单个迁移活动中迁移的数据库数目限制为 4 个。
