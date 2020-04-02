---
title: 逻辑解码 - Azure 数据库，用于 PostgreSQL - 单个服务器
description: 描述逻辑解码和 wal2json，用于在 Azure 数据库中为 PostgreSQL - 单服务器捕获更改数据
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522142"
---
# <a name="logical-decoding"></a>逻辑解码
 
[PostgreSQL 中的逻辑解码](https://www.postgresql.org/docs/current/logicaldecoding.html)允许您将数据更改流式传输到外部使用者。 逻辑解码通常用于事件流和更改数据捕获方案。

逻辑解码使用输出插件将 Postgres 的写入前日志 （WAL） 转换为可读格式。 用于 PostgreSQL 的 Azure 数据库提供了两个输出插件[：test_decoding](https://www.postgresql.org/docs/current/test-decoding.html)和[wal2json](https://github.com/eulerto/wal2json)。
 

> [!NOTE]
> 逻辑解码在 Azure 数据库上的公共预览版中，用于 PostgreSQL - 单服务器。


## <a name="set-up-your-server"></a>设置服务器
要开始使用逻辑解码，请使服务器保存和流式传输 WAL。 

1. 将 azure.replication_support`logical`设置为使用 Azure CLI。 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > 如果使用读取副本，azure.replication_support 设置为`logical`也允许运行副本。 如果停止使用逻辑解码，则将设置更改回`replica`。 


2. 重新启动服务器以应用这些更改。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>开始逻辑解码

逻辑解码可以通过流式处理协议或 SQL 接口使用。 这两种方法都使用[复制槽](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)。 槽表示来自单个数据库的更改流。

使用复制槽需要 Postgres 的复制权限。 此时，复制权限仅适用于服务器的管理员用户。 

### <a name="streaming-protocol"></a>流式处理协议
使用流式处理协议进行更改通常更可取。 您可以创建自己的消费者/连接器，或使用[像 Debezium](https://debezium.io/)这样的工具。 

访问 wal2json 文档，了解[使用流协议与pg_recvlogical 的示例](https://github.com/eulerto/wal2json#pg_recvlogical)。


### <a name="sql-interface"></a>SQL 接口
在下面的示例中，我们将 SQL 接口与 wal2json 插件一起使用。
 
1. 创建插槽。
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

4. 使用完成后，将插槽放下。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>监控插槽

您必须监视逻辑解码。 必须删除任何未使用的复制槽。 插槽会保留 Postgres WAL 日志和相关系统目录，直到使用者读取更改。 如果使用者失败或未正确配置，则未使用的日志将堆积并填满您的存储。 此外，未消耗的日志会增加事务 ID 环绕的风险。 这两种情况都可能导致服务器不可用。 因此，持续使用逻辑复制槽至关重要。 如果不再使用逻辑复制槽，则立即将其删除。

pg_replication_slots视图中的"活动"列将指示是否有使用者连接到插槽。
```SQL
SELECT * FROM pg_replication_slots;
```

在*使用的存储*上[设置警报](howto-alert-on-metric.md)，并在*副本指标上设置最大延迟*，以便在值超过正常阈值时通知您。 

> [!IMPORTANT]
> 必须删除未使用的复制槽。 否则可能导致服务器不可用。

## <a name="how-to-drop-a-slot"></a>如何放置插槽
如果不主动使用复制槽，则应将其丢弃。

要删除使用 SQL`test_slot`调用的复制槽，
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 如果停止使用逻辑解码，则将 azure.replication_support更改回`replica``off`或 。 保留的`logical`WAL 详细信息更加详细，在不使用逻辑解码时应禁用。 

 
## <a name="next-steps"></a>后续步骤

* 访问 Postgres 文档，[了解有关逻辑解码的更多详细信息](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 如果您对逻辑解码有疑问，则与[我们的团队](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)联系。
* 了解有关[读取副本](concepts-read-replicas.md)的更多内容。

