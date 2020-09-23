---
title: 逻辑解码 - Azure Database for PostgreSQL - 单一服务器
description: 介绍用于 Azure Database for PostgreSQL - 单一服务器中的变更数据捕获的逻辑解码和 wal2json
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: bd886bea90c1092e38fac191a60a118aab0bef1f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903887"
---
# <a name="logical-decoding"></a>逻辑解码
 
使用 [PostgreSQL 中的逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding.html)可将数据更改流式传输到外部使用者。 逻辑解码广泛用于事件流和变更数据捕获方案。

逻辑解码使用一个输出插件将 Postgres 的预写日志 (WAL) 转换为可读格式。 Azure Database for PostgreSQL 提供以下输出插件：[wal2json](https://github.com/eulerto/wal2json)、[test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) 和 pgoutput。 pgoutput 由 Postgres 10 及更高版本中的 Postgres 提供。

有关 Postgres 逻辑解码工作原理的概述，请 [访问我们的博客](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)。 

> [!NOTE]
> Azure Database for PostgreSQL - 单一服务器上的逻辑解码目前为公共预览版。


## <a name="set-up-your-server"></a>设置服务器 
逻辑解码和[只读副本](concepts-read-replicas.md)都依赖于 Postgres 预写日志 (WAL) 来获取信息。 这两个功能需要使用来自 Postgres 的不同级别的日志记录。 逻辑解码需要的日志记录的级别比只读副本需要的更高。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **关闭** - 在 WAL 中包含最少的信息。 大多数 Azure Database for PostgreSQL 服务器上都不提供此设置。  
* **副本** - 比“关闭”详细。 这是运行[只读副本](concepts-read-replicas.md)所需的最低日志记录级别。 此设置是大多数服务器上的默认设置。
* **逻辑** - 比“副本”详细。 这是运行逻辑解码所需的最低日志记录级别。 使用此设置时，只读副本也可以运行。

更改此参数后，需要重启服务器。 在内部，此参数设置 Postgres 参数 `wal_level`、`max_replication_slots` 和 `max_wal_senders`。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 将 azure.replication_support 设置为 `logical`。
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. 重启服务器以应用更改。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

### <a name="using-azure-portal"></a>使用 Azure 门户

1. 将 Azure 复制支持设置为“逻辑”。 选择“保存” 。

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL - 复制 - Azure 复制支持":::

2. 通过选择“是”，重启服务器以应用更改。

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL - 复制 - 确认重启":::


## <a name="start-logical-decoding"></a>开始逻辑解码

可以通过流式处理协议或 SQL 接口使用逻辑解码。 这两种方法都使用[复制槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)。 槽表示来自单一数据库的更改流。

使用复制槽需要 Postgres 的复制特权。 目前，复制特权仅适用于服务器的管理员用户。 

### <a name="streaming-protocol"></a>流式处理协议
我们通常倾向于通过流式处理协议使用更改。 你可以创建自己的使用者/连接器，或者使用 [Debezium](https://debezium.io/) 之类的工具。 

请访问 wal2json 文档，查看[将流式处理协议与 pg_recvlogical 配合使用的示例](https://github.com/eulerto/wal2json#pg_recvlogical)。


### <a name="sql-interface"></a>SQL 接口
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


## <a name="monitoring-slots"></a>监视槽

必须监视逻辑解码。 必须删除任何未使用的复制槽。 在使用者读取更改之前，槽会一直保存到 Postgres WAL 日志和相关的系统目录。 如果使用者失败或者未经正确配置，则未使用的日志将不断堆积，直至填满存储。 此外，未使用的日志会增大事务 ID 换行的风险。 这两种情况可能会导致服务器不可用。 因此，逻辑复制槽被持续使用是至关重要的。 如果不再使用某个逻辑复制槽，请立即将其删除。

pg_replication_slots 视图中的“active”列指示是否有使用者连接到某个槽。
```SQL
SELECT * FROM pg_replication_slots;
```

请针对“已用存储”和“副本的最大滞后时间”指标[设置警报](howto-alert-on-metric.md)，以便在值超过正常阈值时收到通知。  

> [!IMPORTANT]
> 必须删除未使用的复制槽。 否则可能会导致服务器不可用。

## <a name="how-to-drop-a-slot"></a>如何删除槽
如果你未积极使用某个复制槽，应将其删除。

若要使用 SQL 删除名为 `test_slot` 的复制槽：
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果停止使用逻辑解码，请将 azure.replication_support 改回到 `replica` 或 `off`。 通过 `logical` 保留的 WAL 详细信息更详尽，应在逻辑解码未在使用时被禁用。 

 
## <a name="next-steps"></a>后续步骤

* 访问 Postgres 文档来[详细了解逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 如果在逻辑解码方面有任何问题，请联系[我们的团队](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)。
* 详细了解[只读副本](concepts-read-replicas.md)。

