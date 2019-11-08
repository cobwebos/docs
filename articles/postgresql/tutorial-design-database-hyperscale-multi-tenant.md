---
title: 使用 Azure Database for PostgreSQL 设计多租户数据库 - 超大规模 (Citus) 教程
description: 本教程介绍如何在 Azure Database for PostgreSQL 超大规模 (Citus) 中创建、填充和查询分布式表。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 130c3e9f5abb24ffcc4e0c4ad6b96af5fca62090
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496542"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>教程：通过使用 Azure Database for PostgreSQL - 超大规模 (Citus)设计多租户数据库

在本教程中，你将了解如何使用 Azure Database for PostgreSQL - 超大规模 (Citus) 来执行以下操作：

> [!div class="checklist"]
> * 创建 Hyperscale (Citus) 服务器组
> * 使用 psql 实用工具创建架构
> * 节点中的分片表
> * 引入示例数据
> * 查询租户数据
> * 租户之间共享数据
> * 自定义每租户架构

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>使用 psql 实用工具创建架构

使用 psql 连接到 Azure Database for PostgreSQL - 超大规模 (Citus)后，可以完成一些基本任务。 本教程将指导你创建 Web 应用，该应用允许广告厂商跟踪他们的广告系列。

多家公司可以使用该应用，让我们创建一个表来记录这些公司，创建另一个表来记录他们的广告系列。 在 psql 控制台中，运行这些命令：

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

每个广告系列会支付运行广告的费用。 此外，添加用于广告的一个表，方法是在以上代码后，运行以下代码：

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

最后，我们将跟踪有关每个广告的点击次数和展示次数的统计信息：

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

现在，你可以在 psql 中看到表列表中新建的表，方法是运行：

```postgres
\dt
```

多租户应用程序仅可以对每个租户强制实施唯一性，因此所有主键和外键包含公司 ID。

## <a name="shard-tables-across-nodes"></a>节点中的分片表

超大规模部署基于用户指定的列的值存储不同节点上的表行。 此“分布列”标记哪个租户拥有哪些行。

让我们将分布列设置为 company\_id，即租户标识符。 在 Psql 中，运行这些函数：

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>引入示例数据

现在，在 psql 外部，在常规命令行中，下载示例数据集：

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

返回到 psql 内部，大容量加载数据。 请务必在下载数据文件的目录中运行 psql。

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

现在，此数据将跨工作器节点分布。

## <a name="query-tenant-data"></a>查询租户数据

当应用程序请求单个租户的数据时，数据库可以在单个工作器节点上执行查询。 单租户查询根据单个租户 ID 进行筛选。 例如，以下查询针对 `company_id = 5` 筛选广告和展示次数。 尝试在 psql 中运行它并查看结果。

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>租户之间共享数据

到目前为止，已按照 `company_id` 分发所有表，但一些数据不会“自然地”属于任何租户，并且这些数据可以共享。 例如，示例广告平台中的所有公司可能需要获取基于 IP 地址的受众地理位置信息。

创建一个表来记录共享地理位置信息。 在 psql 中运行以下命令：

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

接下来，将 `geo_ips` 设置为“引用表”，存储每个工作器节点上的表副本。

```sql
SELECT create_reference_table('geo_ips');
```

将它和示例数据一同加载。 请记住要从下载数据集的目录内部运行此命令。

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

将点击次数表和 geo\_ips 联接，这在所有节点上都是高效的。
下面是查找点击广告的所有人的位置的联接
290. 请尝试在 psql 中运行查询。

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>自定义每租户架构

每个租户可能需要存储他人不需要的特殊信息。 但是，所有租户都共享具有相同数据库架构的同一基础结构。 额外的数据可以到哪儿去？

一个技巧是使用开放式列类型，例如 PostgreSQL 的 JSONB。  我们的架构在 `clicks` 中有一个 JSONB 字段，称为 `user_data`。
一家公司（例如 company five）可以使用列来跟踪用户是否使用移动设备。

以下查询用于发现是移动设备访客还是传统访客的点击次数更高。

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

可以为单个公司优化此查询，方法是创建[部分索引](https://www.postgresql.org/docs/current/static/indexes-partial.html)。

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

更概括地说，我们可以在列中的每个键和值上创建 [GIN 索引](https://www.postgresql.org/docs/current/static/gin-intro.html)。

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你已在服务器组中创建了 Azure 资源。 如果你认为以后不需要这些资源，请删除该服务器组。 在服务器组的“概述”页中，按“删除”按钮   。 弹出页面上出现提示时，请确认服务器组的名称，然后单击最后一个“删除”按钮  。

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何预配 Hyperscale (Citus) 服务器组。 使用 psql 连接它，创建架构和分布式数据。 你已了解如何在租户中和租户之间查询数据，以及如何自定义每租户架构。

接下来，了解有关超大规模的概念。
> [!div class="nextstepaction"]
> [超大规模节点类型](https://aka.ms/hyperscale-concepts)
