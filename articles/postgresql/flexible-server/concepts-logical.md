---
title: 逻辑复制和逻辑解码-Azure Database for PostgreSQL-灵活服务器
description: 了解如何在 Azure Database for PostgreSQL 灵活的服务器中使用逻辑复制和逻辑解码
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707857"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-灵活服务器中的逻辑复制和逻辑解码

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

对于 Postgres 版本11，Azure Database for PostgreSQL 灵活的服务器支持 PostgreSQL 的逻辑复制和逻辑解码功能。

## <a name="comparing-logical-replication-and-logical-decoding"></a>比较逻辑复制和逻辑解码
逻辑复制和逻辑解码具有几个不同之处。 它们
* 允许你将数据复制到 Postgres
* 使用 [预写日志 (WAL) ](https://www.postgresql.org/docs/current/wal.html) 作为更改源
* 使用 [逻辑复制槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) 来发送数据。 槽表示更改流。
* 使用表的 " [副本标识" 属性](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) 来确定可以发送哪些更改
* 不复制 DDL 更改


这两种技术的不同之处在于：逻辑复制 
* 允许您指定要复制的一个或一组表
* 在 PostgreSQL 实例之间复制数据

逻辑解码 
* 提取数据库中所有表的更改 
* 无法在 PostgreSQL 实例之间直接发送数据


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>逻辑复制和逻辑解码的先决条件

1. 将服务器参数设置 `wal_level` 为 `logical` 。
2. 重新启动服务器以应用 `wal_level` 更改。
3. 确认 PostgreSQL 实例允许来自连接资源的网络流量。
4. 授予管理员用户复制权限。
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>使用逻辑复制和逻辑解码

### <a name="logical-replication"></a>逻辑复制
逻辑复制使用术语 "发布服务器" 和 "订阅服务器"。 
* 发布服务器是要 **从**其发送数据的 PostgreSQL 数据库。 
* 订阅服务器是要 **向**其发送数据的 PostgreSQL 数据库。

下面是可用于尝试逻辑复制的一些示例代码。

1. 连接到发布服务器数据库。 创建表并添加一些数据。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. 为表创建发布。
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. 连接到订阅服务器数据库。 使用与发布服务器相同的架构创建一个表。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 创建将连接到之前创建的发布的订阅。
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. 你现在可以在订阅服务器上查询表。 你会看到它已从发布服务器接收数据。
   ```SQL
   SELECT * FROM basic;
   ```

您可以将更多的行添加到发布服务器的表中，并查看订阅服务器上的更改。

请访问 PostgreSQL 文档以了解有关 [逻辑复制](https://www.postgresql.org/docs/current/logical-replication.html)的详细信息。

### <a name="logical-decoding"></a>逻辑解码
可以通过流式处理协议或 SQL 接口使用逻辑解码。 

#### <a name="streaming-protocol"></a>流式处理协议
我们通常倾向于通过流式处理协议使用更改。 你可以创建自己的使用者/连接器，或使用第三方服务（如 [Debezium](https://debezium.io/)）。 

请访问 wal2json 文档，查看[将流式处理协议与 pg_recvlogical 配合使用的示例](https://github.com/eulerto/wal2json#pg_recvlogical)。

#### <a name="sql-interface"></a>SQL 接口 
以下示例将 SQL 接口与 wal2json 插件配合使用。
 
1. 创建槽。
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. 发出 SQL 命令。 例如：
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 使用更改。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   输出如下所示：
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 用完槽后，请将其删除。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

请访问 PostgreSQL 文档以了解有关 [逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding.html)的详细信息。


## <a name="monitoring"></a>监视
必须监视逻辑解码。 必须删除任何未使用的复制槽。 在读取更改之前，槽保存到 Postgres WAL 日志和相关的系统目录。 如果你的订阅者或使用者发生故障，或者未正确配置，则未使用的日志将会堆积并填充你的存储。 此外，未使用的日志会增大事务 ID 换行的风险。 这两种情况可能会导致服务器不可用。 因此，逻辑复制槽被持续使用是至关重要的。 如果不再使用某个逻辑复制槽，请立即将其删除。

pg_replication_slots 视图中的“active”列指示是否有使用者连接到某个槽。
```SQL
SELECT * FROM pg_replication_slots;
```

针对**使用的最大事务 id**和**存储**[设置警报](howto-alert-on-metrics.md)，以在值超过正常阈值时通知你。 

## <a name="limitations"></a>限制
* **读取副本** -对灵活的服务器当前不支持 Azure Database for PostgreSQL 读取副本。
* **槽和 HA 故障转移** -主服务器上的逻辑复制槽在辅助 AZ 中的备用服务器上不可用。 如果服务器使用区域冗余高可用性选项，则此选项适用于你。 如果故障转移到备用服务器，则不会在备用服务器上使用逻辑复制槽。

## <a name="next-steps"></a>后续步骤
* 了解有关[网络选项](concepts-networking.md)的详细信息
* 了解适用于灵活服务器的[扩展](concepts-extensions.md)
* 了解有关[高可用性](concepts-high-availability.md)的详细信息

