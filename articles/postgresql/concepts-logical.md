---
title: 逻辑解码-Azure Database for PostgreSQL-单服务器
description: 介绍 Azure Database for PostgreSQL-单服务器中的变更数据捕获的逻辑解码和 wal2json
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522142"
---
# <a name="logical-decoding"></a>逻辑解码
 
通过[PostgreSQL 中的逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding.html)，你可以将数据更改流式传输到外部使用者。 逻辑解码是一般用于事件流和更改数据捕获方案。

逻辑解码使用输出插件将 Postgres 的预写日志（WAL）转换为可读的格式。 Azure Database for PostgreSQL 提供了两个输出插件： [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html)和[wal2json](https://github.com/eulerto/wal2json)。
 

> [!NOTE]
> 逻辑解码在 Azure Database for PostgreSQL-单服务器上的公共预览版中。


## <a name="set-up-your-server"></a>设置服务器
若要开始使用逻辑解码，请使服务器能够保存并流式传输 WAL。 

1. `logical`使用 Azure CLI 设置 replication_support。 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > 如果使用 "读取副本"，则 azure. replication_support `logical`设置为也允许副本运行。 如果停止使用逻辑解码，请将设置改回`replica`。 


2. 重新启动服务器以应用这些更改。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>开始逻辑解码

可以通过流式处理协议或 SQL 接口使用逻辑解码。 这两种方法都使用[复制槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)。 槽表示来自单个数据库的更改流。

使用复制槽需要 Postgres 的复制权限。 此时，复制权限仅适用于服务器的管理员用户。 

### <a name="streaming-protocol"></a>流式处理协议
使用流式处理协议的更改通常更可取。 你可以创建自己的使用者/连接器，或使用类似于[Debezium](https://debezium.io/)的工具。 

有关将[流式处理协议与 pg_recvlogical 结合使用的示例](https://github.com/eulerto/wal2json#pg_recvlogical)，请访问 wal2json 文档。


### <a name="sql-interface"></a>SQL 接口
在下面的示例中，我们将 SQL 接口与 wal2json 插件结合使用。
 
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

3. 使用这些更改。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   输出将如下所示：
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

4. 使用完槽后，请将其删除。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>监视槽

必须监视逻辑解码。 必须删除任何未使用的复制槽。 槽保存到 Postgres WAL 日志和相关的系统目录，直到使用者读取了更改。 如果使用者发生故障或未正确配置，则未使用的日志将会堆积并填充存储。 此外，未用到的日志会增加事务 ID 环绕的风险。 这两种情况都可能会导致服务器不可用。 因此，连续使用逻辑复制槽至关重要。 如果不再使用逻辑复制槽，请立即将其删除。

Pg_replication_slots 视图中的 "活动" 列将指示是否存在连接到槽的使用者。
```SQL
SELECT * FROM pg_replication_slots;
```

对所*用的存储*和副本度量值的*最大延迟*[设置警报](howto-alert-on-metric.md)，以便在值超过正常阈值时通知您。 

> [!IMPORTANT]
> 必须删除未使用的复制槽。 否则，可能会导致服务器不可用。

## <a name="how-to-drop-a-slot"></a>如何删除槽
如果你没有主动使用复制槽，则应该将其删除。

使用 SQL 删除名`test_slot`为的复制槽：
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果停止使用逻辑解码，请将 azure 改回 replication_support `replica`或。 `off` 保留`logical`的 WAL 详细信息更详细，如果未使用逻辑解码，则应将其禁用。 

 
## <a name="next-steps"></a>后续步骤

* 请访问 Postgres 文档以[了解有关逻辑解码的详细信息](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 如果你对逻辑解码有疑问，请联系[我们的团队](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)。
* 了解更多有关[读取副本](concepts-read-replicas.md)的信息。

