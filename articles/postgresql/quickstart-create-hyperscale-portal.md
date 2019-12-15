---
title: 创建分布式表 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本快速入门介绍如何在 Azure Database for PostgreSQL 超大规模 (Citus) 中创建和查询分布式表。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973403"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure Database for PostgreSQL - 超大规模 (Citus)

用于 PostgreSQL 的 Azure 数据库是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 本快速入门介绍如何使用 Azure 门户创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器组。 你将探究分布式数据：在节点之间将表分片、引入示例数据，以及运行在多个节点上执行的查询。

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>创建和分布表

使用 psql 连接到 Hyperscale 协调器节点后，可以完成一些基本任务。

Hyperscale 服务器中有三种类型的表：

- 分布式表或分片表（分散在不同的位置以帮助进行缩放，从而提高性能并便于并行化）
- 引用表（保留多个副本）
- 本地表（通常用于内部管理表）

本快速入门重点介绍分布式表，以帮助读者熟悉此类表。

我们要使用的数据模型非常简单：来自 GitHub 的用户和事件数据。 事件包括分叉创建、组织相关的 git 提交，等等。

通过 psql 建立连接后，让我们来创建表。 在 psql 控制台中运行：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` 的 `payload` 字段包含 JSONB 数据类型。 JSONB 是 Postgres 中采用二进制格式的 JSON 数据类型。 使用此数据类型，可以方便在单个列中存储灵活的架构。

Postgres 可以基于此类型创建 `GIN` 索引，以便为表中的每个键和值编制索引。 通过索引可以使用各种条件快速轻松地查询有效负载。 在加载数据之前，让我们继续创建几个索引。 在 psql 中：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

接下来，我们将这些 Postgres 表放到协调器节点上，并告知 Hyperscale 在工作器节点之间将这些表分片。 为此，我们将针对每个表运行一个查询，指定将该表分片所依据的键。 在当前示例中，我们要将 `user_id` 上的事件和用户表分片：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

现已准备好加载数据。 仍然在 psql 中，执行 shell 命令来下载文件：

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

接下来，将数据从文件加载到分布式表中：

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>运行查询

接下来的操作比较有趣，但实际上只是运行几个查询。 让我们从一个简单的 `count (*)` 着手，以查看加载的数据量：

```sql
SELECT count(*) from github_events;
```

查询非常顺利。 稍后我们会花点时间来了解聚合，但我们先看看其他几个查询。 JSONB `payload` 列中包含许多数据，但具体的数据根据事件类型而异。 `PushEvent` 事件包含一个大小，它反映了推送操作的非重复提交次数。 我们可以使用该大小找到每小时的提交总次数：

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

到目前为止，查询只是涉及到 github\_events，但我们可将这些信息与 github\_users 合并。 由于我们基于同一个标识符 (`user_id`) 分片了用户和事件，因此，这两个表中具有匹配用户 ID 的行将[共置](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)到同一个数据库节点，并可以轻松联接。

如果在 `user_id` 上联接，则 Hyperscale 可将联接执行下推到分片中，以便在工作器节点上并行执行。 例如，让我们查找创建了最多存储库的用户：

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你已在服务器组中创建了 Azure 资源。 如果你认为以后不需要这些资源，请删除该服务器组。 在服务器组的“概述”页中，按“删除”按钮   。 弹出页面上出现提示时，请确认服务器组的名称，然后单击最后一个“删除”按钮  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何预配 Hyperscale (Citus) 服务器组。 你已使用 psql 连接到该组，创建了架构并分布了数据。

接下来，请遵循以下教程生成可缩放的多租户应用程序。
> [!div class="nextstepaction"]
> [设计多租户数据库](https://aka.ms/hyperscale-tutorial-multi-tenant)
