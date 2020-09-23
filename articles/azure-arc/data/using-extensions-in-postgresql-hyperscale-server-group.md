---
title: 使用 PostgreSQL 扩展
description: 使用 PostgreSQL 扩展
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19b2ec283619df0cc8d3c880cb2df6f53f6fb332
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934316"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在启用了 Azure Arc 的 PostgreSQL 超大规模服务器组中使用 PostgreSQL 扩展

如果将其用于扩展，PostgreSQL 的效果最佳。 事实上，我们自己的超大规模功能的一个关键元素是默认安装的 Microsoft 提供的 `citus` 扩展，它允许 Postgres 以透明方式在多个节点间分片数据。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>扩展列表
除了中的扩展外 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) ，启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的容器中存在的扩展列表为：
- `citus`，v：9。4
- `pg_cron`，v：1。2
- `plpgsql`，v：1。0
- `postgis`，v：3.0。2

此列表将不断演变，并将在本文档中发布更新。 除了上面列出的扩展名外，还不可能添加扩展。

本指南将介绍如何使用其中两个扩展：
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>管理扩展

### <a name="enable-extensions"></a>启用扩展
对于属于的扩展，不需要执行此步骤 `contrib` 。
启用扩展的命令的常规格式为：

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>在服务器组的创建时间启用扩展：
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>对已存在的实例启用扩展：
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>获取已启用的扩展列表：
运行以下命令之一。

##### <a name="with-azdata"></a>With azdata
```console
azdata arc postgres server show -n <server group name>
```
滚动输出，并注意服务器组规范中的 engine\extensions 部分。 例如：
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>With kubectl
```console
kubectl describe postgresql-12s/postgres02
```
滚动输出，并注意服务器组规范中的 engine\extensions 部分。 例如：
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>创建扩展：
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>获取在服务器组中创建的扩展列表：
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>从服务器组中删除扩展：
用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>使用 PostGIS 和 Pg_cron 扩展

### <a name="the-postgis-extension"></a>PostGIS 扩展

可以在现有服务器组上启用 PostGIS 扩展，也可以创建一个已启用扩展的新扩展：

**在服务器组的创建时启用扩展：**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**对已存在的实例启用扩展：**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

若要验证安装了哪些扩展，请在使用常用的 PostgreSQL 客户端工具（如 Azure Data Studio）连接到实例后使用以下标准 PostgreSQL 命令：
```console
select * from pg_extension;
```

对于 PostGIS 示例，先从 MIT 的城市部的城市研究中获取 [示例数据](http://duspviz.mit.edu/tutorials/intro-postgis/) & 计划。 `apt-get install unzip`使用 VM 进行测试时，可能需要运行以安装解压缩。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

接下来，请连接到数据库，并创建 PostGIS 扩展：

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 如果要使用包中的某个扩展 `postgis` (例如 `postgis_raster` ， `postgis_topology` ) ， `postgis_sfcgal` `fuzzystrmatch` 则需要先创建 postgis 扩展，然后再创建另一个扩展。 例如： `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

并创建架构：

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

现在，我们可以将 PostGIS 与 scale out 功能组合起来，并将 coffee_shops 表分散起来：

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

让我们加载一些数据：

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

并 `geom` 在 PostGIS 数据类型中用正确编码的纬度和经度填充字段 `geometry` ：

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

现在，我们可以列出最接近 MIT (77 马萨诸塞州的咖啡店，42.359055，-71.093500) ：

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Pg_cron 扩展

`pg_cron`除了 PostGIS，让我们在 PostgreSQL 服务器组上启用：

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

请注意，这会重启节点并安装其他扩展，这可能需要 2-3 分钟。

现在，我们可以再次连接，并创建 `pg_cron` 扩展：

```sql
CREATE EXTENSION pg_cron;
```

对于测试目的，允许创建一个表，该表 `the_best_coffee_shop` 采用前面表中的随机名称 `coffee_shops` ，并设置表内容：

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

我们可以使用 `cron.schedule` 另外几个 SQL 语句来获取随机表名称 (请注意，使用临时表来存储分布式查询结果) ，并将其存储在中 `the_best_coffee_shop` ：

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

现在，一分钟后，我们将获得一个不同的名称：

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

有关语法的完整详细信息，请参阅 [PG_CRON 自述文件](https://github.com/citusdata/pg_cron) 。

>[!NOTE]
>不支持删除 `citus` 扩展。 `citus`需要扩展才能提供超大规模体验。

