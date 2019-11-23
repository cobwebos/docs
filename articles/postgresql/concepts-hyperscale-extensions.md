---
title: Azure Database for PostgreSQL 中的 PostgreSQL 扩展–超大规模（Citus）
description: 介绍使用 Azure Database for PostgreSQL 中的扩展来扩展数据库功能的功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 41edcc40bae7915e321a278592dd2f73621aae05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482414"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 中的 PostgreSQL 扩展–超大规模（Citus）

PostgreSQL 提供使用扩展来扩展数据库功能的功能。 扩展允许在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载后，扩展可以像内置功能那样运行。 有关 PostgreSQL 扩展的详细信息，请参阅 [将相关对象打包到扩展](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)中。

## <a name="use-postgresql-extensions"></a>使用 PostgreSQL 扩展

必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请从 psql 工具运行 [CREATE extension](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) 命令，将打包的对象加载到数据库中。

Azure Database for PostgreSQL 超大规模（Citus）目前支持此处列出的密钥扩展的子集。 不支持所列扩展以外的扩展。 不能 Azure Database for PostgreSQL 创建自己的扩展。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 支持的扩展

下表列出了用于 PostgreSQL 的 Azure 数据库目前支持的标准 PostgreSQL 扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。

### <a name="data-types-extensions"></a>数据类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 提供不区分大小写的字符串类型。 |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 提供用于多维数据集的数据类型。 |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 提供用于存储键/值对集的数据类型。 |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 提供用于国际产品编号标准的数据类型。 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | 大型对象维护。 |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 提供用于分层树形结构的数据类型。 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | 用于表示线段或浮点间隔的数据类型。 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Top-n JSONB 的类型。 |

### <a name="full-text-search-extensions"></a>全文搜索扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 提供用于整数的文本搜索字典模板。 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 用于扩展同义词处理的文本搜索字典模板。 |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 删除了词素中重音（附加符号）的文本搜索字典。 |

### <a name="functions-extensions"></a>函数扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 用于自动递增字段的函数。 |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 提供一种计算地球表面上的大圆距离的方法。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 提供多个函数，用于确定字符串间的相似性和差异。 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 用于跟踪谁更改了表的函数。 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | 整数聚合器和枚举器（已过时）。 |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 提供用于操作无 null 整数数组的函数和运算符。 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 用于跟踪上次修改时间的函数。 |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 提供加密函数。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 按时间或 ID 管理已分区表。 |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 提供函数和运算符，用于基于三元匹配确定字母数字文本的相似性。 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 用于实现引用完整性（已过时）的函数。 |
> | session\_analytics | 用于查询 hstore 数组的函数。 |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 提供可操作整个表（包括交叉表）的函数。 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 触发的更改通知。 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 用于实现旅行的函数。 |
> | [uuid ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 生成全局唯一标识符 (UUID)。 |

### <a name="hyperscale-extensions"></a>超大规模扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus 分布式数据库。 |
> | shard\_rebalancer | 在添加或删除节点时，安全地重新平衡服务器组中的数据。 |

### <a name="index-types-extensions"></a>索引类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [布隆](https://www.postgresql.org/docs/current/bloom.html) | 布隆访问方法基于签名文件的索引。 |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 提供了示例 GIN 运算符类，这些类可实现某些数据类型的类似于 B 树的行为。 |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | 提供实施 B-tree 的 GiST 索引运算符类。 |

### <a name="language-extensions"></a>语言扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 可加载过程语言。 |

### <a name="miscellaneous-extensions"></a>其他扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL 的管理功能。 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 用于验证关系完整性的函数。 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 用于平面文件访问的外数据包装。 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 在较低级别检查数据库页的内容。 |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 提供一种方法用于实时检查共享缓冲区缓存的当前状况。 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL 的作业计划程序。 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 检查可用空间图（FSM）。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 提供一种方法用于将相关数据加载到缓冲区缓存中。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 提供一种方法用于跟踪服务器执行的所有 SQL 语句的执行统计信息。 有关此扩展的信息，请参阅 "pg_stat_statements" 部分。 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | 检查可见性地图（VM）和页面级别的可见性信息。 |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 提供一种显示行级锁定信息的方法。 |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 提供一种显示元组级别统计信息的方法。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 外部数据包装器，用于访问外部 PostgreSQL 服务器中存储的数据。 有关此扩展的信息，请参阅 "dblink and postgres_fdw" 部分。|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | 有关 SSL 证书的信息。 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE 方法，它接受行数作为限制。 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE 方法，以毫秒为单位接受时间（以毫秒为单位）。 |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | 提供了一种创建不耗费 CPU 或磁盘的假设索引的方法。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 一个支持从数据库会话中连接到其他 PostgreSQL 数据库的模块。 有关此扩展的信息，请参阅 "dblink and postgres_fdw" 部分。 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 查询和 XSLT。 |


### <a name="postgis-extensions"></a>PostGIS 扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL 的空间和地理对象。 |
> | address\_standardizer, address\_standardizer\_data\_us | 用于将地址分析成构成元素。 用于支持地理编码地址规范化步骤。 |
> | postgis\_sfcgal | PostGIS SFCGAL 函数。 |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder and reverse geocoder。 |
> | postgis\_拓扑 | PostGIS 拓扑空间类型和函数。 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg\_stat\_语句扩展](https://www.postgresql.org/docs/current/pgstatstatements.html)在每个 Azure Database for PostgreSQL 服务器上预加载，为你提供一种跟踪 SQL 语句执行统计信息的方法。

设置 `pg_stat_statements.track` 控制扩展对哪些语句进行计数。 默认为 `top`，这意味着将跟踪客户端直接发出的所有语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 作为服务器参数进行配置。

Pg_stat_statements 提供的查询执行信息和对服务器性能的影响，因为它记录了每个 SQL 语句。 如果你没有主动使用 pg_stat_statements 扩展，我们建议你将 `pg_stat_statements.track` 设置为 "`none`"。 某些第三方监视服务可能依赖于 pg_stat_statements 来提供查询性能见解，因此请确认这是否适用于这种情况。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
可以使用 dblink 和 postgres_fdw 从一台 PostgreSQL 服务器连接到另一台服务器，或连接到同一服务器上的其他数据库。 接收服务器需要允许来自发送服务器的连接通过其防火墙。 若要使用这些扩展在 Azure Database for PostgreSQL 服务器之间进行连接，请将 "**允许访问 Azure 服务**" 设置为 "开"。 如果要使用这些扩展来循环回到同一服务器，还需要启用此设置。 "**允许访问 Azure 服务**" 设置可在 Postgres 服务器的 "Azure 门户" 页中的 "**连接安全性**" 下找到。 启用 "允许对所有 Azure Ip 使用白名单**访问 azure 服务**"。

目前，不支持来自 Azure Database for PostgreSQL 的出站连接，与其他 Azure Database for PostgreSQL 服务器的连接除外。
