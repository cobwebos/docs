---
title: 使用 Azure Database for PostgreSQL 设计实时仪表板 – Hyperscale (Citus)（预览版）教程
description: 本教程介绍如何在 Azure Database for PostgreSQL Hyperscale (Citus)（预览版）中创建、填充和查询分布式表。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791305"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>教程：使用 Azure Database for PostgreSQL 设计实时分析仪表板 – Hyperscale (Citus)（预览版）

在本教程中，你将了解如何使用 Azure Database for PostgreSQL - Hyperscale (Citus)（预览版）来执行以下操作：

> [!div class="checklist"]
> * 创建 Hyperscale (Citus) 服务器组
> * 使用 psql 实用工具创建架构
> * 在节点之间将表分片
> * 生成示例数据
> * 执行汇总
> * 查询原始数据和聚合数据
> * 使数据过期

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 实用工具创建架构

使用 psql 连接到 Azure Database for PostgreSQL - Hyperscale (Citus)（预览版）后，可以完成一些基本任务。 本教程将引导你从 Web 分析引入流量数据，然后汇总数据以基于这些数据提供实时仪表板。

让我们创建一个使用所有原始 Web 流量数据的表。 在 psql 终端中运行以下命令：

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

我们还要创建一个表用于保存每分钟的聚合，并创建一个表用于保留上次汇总数据的位置。 还在 psql 中运行以下命令：

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

现在，可以使用以下命令在表列表中查看新建的表：

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>在节点之间将表分片

超大规模部署基于用户指定的列的值存储不同节点上的表行。 此“分布列”标识数据在节点之间的分片方式。

让我们将分布列设置为 site\_id，即分片键。 在 psql 中运行以下函数：

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>生成示例数据

现在，我们的服务器组应已准备好引入一些数据。 可以通过 `psql` 连接在本地运行以下命令，以持续插入数据。

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

该查询会每秒插入大约八行。 系统会根据分布列 `site_id` 的指示，在不同的工作器节点上存储行。

   > [!NOTE]
   > 让数据生成查询保持运行，并针对本教程中所述的剩余命令打开另一个 psql 连接。
   >

## <a name="query"></a>Query

使用超大规模托管选项可让多个节点并行处理查询以加快速度。 例如，数据库可在工作器节点上计算 SUM 和 COUNT 等聚合，并将结果合并成最终的应答。

以下查询统计每分钟的 Web 请求数并提供一些统计信息。
请尝试在 psql 中运行此查询并观察结果。

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>汇总数据

前面的查询在早期阶段可顺利运行，但随着数据的不断增加，其性能将会降级。 即使使用分布式处理，预先计算数据也要比反复重新计算数据更快。

可以通过定期将原始数据汇总到聚合表，来确保仪表板保持快速工作状态。 可以尝试聚合持续时间。 我们使用了每分钟聚合表，但可以改为将数据拆分为 5、15 或 60 分钟。

为了更轻松地运行此汇总，我们将它放入 plpgsql 函数中。 在 psql 中运行以下命令来创建 `rollup_http_request` 函数。

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

创建函数后，请执行它来汇总数据：

```sql
SELECT rollup_http_request();
```

将数据放入预先聚合的表单后，可以查询汇总表以获取与前面相同的报告。 运行以下查询：

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>使旧数据过期

汇总可以加快查询的速度，但我们仍需将旧数据过期，以免存储成本超限。 确定要将每个粒度的数据保留多长时间，并使用标准查询来删除已过期的数据。 在以下示例中，我们决定将原始数据保留一天，并在一个月中每隔一分钟聚合一次数据：

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

在生产环境中，可将这些查询包装在某个函数中，并在 cron 作业中每隔一分钟调用该函数。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你已在服务器组中创建了 Azure 资源。 如果你认为以后不需要这些资源，请删除该服务器组。 在服务器组的“概述”页中，按“删除”按钮   。 弹出页面上出现提示时，请确认服务器组的名称，然后单击最后一个“删除”按钮  。

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何预配 Hyperscale (Citus) 服务器组。 你已使用 psql 连接到该组，创建了架构并分布了数据。 你已了解如何查询原始表单中的数据、定期聚合数据、查询聚合表，并使旧数据过期。

接下来，请了解有关超大规模的概念。
> [!div class="nextstepaction"]
> [超大规模节点类型](https://aka.ms/hyperscale-concepts)