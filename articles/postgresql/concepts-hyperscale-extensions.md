---
title: 扩展 = 超缩放（Citus） - 用于后格雷SQL的 Azure 数据库
description: 描述通过使用 Azure 数据库中的扩展来扩展数据库的功能，用于 PostgreSQL - 超大规模 （Citus）
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485397"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 数据库中的后格雷SQL 扩展，用于后格雷SQL和超大规模（Citus）

PostgreSQL 提供了通过使用扩展来扩展数据库功能的功能。 扩展允许在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载后，扩展可以像内置功能一样运行。 有关 PostgreSQL 扩展的详细信息，请参阅 [将相关对象打包到扩展中](https://www.postgresql.org/docs/current/static/extend-extensions.html)。

## <a name="use-postgresql-extensions"></a>使用 PostgreSQL 扩展

必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 要安装特定扩展，请从 psql 工具运行 [CREATE 扩展](https://www.postgresql.org/docs/current/static/sql-createextension.html) 命令，将打包的对象加载到数据库中。

用于 PostgreSQL - 超大规模 （Citus） 的 Azure 数据库当前支持此处列出的密钥扩展的子集。 不支持列出的扩展以外的扩展。 不能使用 Azure 数据库为 PostgreSQL 创建自己的扩展。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 支持的扩展

下表列出了用于 PostgreSQL 的 Azure 数据库目前支持的标准 PostgreSQL 扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。

### <a name="data-types-extensions"></a>数据类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 提供不区分大小写的字符串类型。 |
> | [立方体](https://www.postgresql.org/docs/current/static/cube.html) | 提供用于多维数据集的数据类型。 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | 提供用于存储键值对集的数据类型。 |
> | [赫尔](https://github.com/citusdata/postgresql-hll) | 提供超日志数据结构。 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 提供用于国际产品编号标准的数据类型。 |
> | [洛](https://www.postgresql.org/docs/current/lo.html) | 大型对象维护。 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 提供用于分层树形结构的数据类型。 |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | 用于表示线段或浮点间隔的数据类型。 |
> | [托普](https://github.com/citusdata/postgresql-topn/) | 类型为顶 n JSONB。 |

### <a name="full-text-search-extensions"></a>全文搜索扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 提供用于整数的文本搜索字典模板。 |
> | [迪克特\_·辛](https://www.postgresql.org/docs/current/dict-xsyn.html) | 用于扩展同义词处理的文本搜索字典模板。 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | 删除了词素中重音（附加符号）的文本搜索字典。 |

### <a name="functions-extensions"></a>函数扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [自动公司](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 用于自动递增字段的函数。 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 提供一种计算地球表面上的大圆距离的方法。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 提供多个函数，用于确定字符串间的相似性和差异。 |
> | [插入\_用户名](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 用于跟踪更改表的人员的功能。 |
> | [因塔格](https://www.postgresql.org/docs/current/intagg.html) | 整数聚合器和枚举器（已过时）。 |
> | [内塔雷](https://www.postgresql.org/docs/current/static/intarray.html) | 提供用于操作无 null 整数数组的函数和运算符。 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 用于跟踪上次修改时间的函数。 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 提供加密函数。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 按时间或 ID 管理已分区表。 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | 提供函数和运算符，用于基于三元匹配确定字母数字文本的相似性。 |
> | [雷菲特](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 实现参考完整性的功能（已过时）。 |
> | 会话\_分析 | 用于查询 hstore 数组的函数。 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | 提供可操作整个表（包括交叉表）的函数。 |
> | [Tcn](https://www.postgresql.org/docs/current/tcn.html) | 触发的更改通知。 |
> | [时间旅行](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 实现时间差的功能。 |
> | [uuid ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | 生成全局唯一标识符 (UUID)。 |

### <a name="hyperscale-extensions"></a>超大规模扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [西图斯](https://github.com/citusdata/citus) | Citus 分布式数据库。 |
> | 分片\_再平衡器 | 在添加或删除节点的情况下，安全地重新平衡服务器组中的数据。 |

### <a name="index-types-extensions"></a>索引类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [绽放](https://www.postgresql.org/docs/current/bloom.html) | 绽放访问方法 - 基于签名文件的索引。 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 提供示例 GIN 运算符类，这些类为某些数据类型实现类似 B 树的行为。 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | 提供实施 B-tree 的 GiST 索引运算符类。 |

### <a name="language-extensions"></a>语言扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 可加载过程语言。 |

### <a name="miscellaneous-extensions"></a>其他扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [管理员包](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL 的管理功能。 |
> | [阿姆切克](https://www.postgresql.org/docs/current/amcheck.html) | 用于验证关系完整性的功能。 |
> | [文件\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 用于平面文件访问的外数据包装器。 |
> | [页检查](https://www.postgresql.org/docs/current/pageinspect.html) | 在较低级别检查数据库页的内容。 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | 提供一种方法用于实时检查共享缓冲区缓存的当前状况。 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL 的工作计划程序。 |
> | [pg\_自由空间图](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 检查可用空间图 （FSM）。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 提供一种方法用于将相关数据加载到缓冲区缓存中。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | 提供一种方法用于跟踪服务器执行的所有 SQL 语句的执行统计信息。 有关此扩展的信息，请参阅"pg_stat_statements"部分。 |
> | [pg\_可见性](https://www.postgresql.org/docs/current/pgvisibility.html) | 检查可见性映射 （VM） 和页面级可见性信息。 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 提供一种显示行级锁定信息的方法。 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | 提供一种显示元组级别统计信息的方法。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 外部数据包装器，用于访问外部 PostgreSQL 服务器中存储的数据。 有关此扩展的信息，请参阅"闪烁和postgres_fdw"部分。|
> | [斯里福林](https://www.postgresql.org/docs/current/sslinfo.html) | 有关 SSL 证书的信息。 |
> | [tsm\_\_系统行](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE 方法，该方法接受行数作为限制。 |
> | [tsm\_\_系统时间](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE 方法，该方法接受以毫秒为单位的时间作为限制。 |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | 提供了一种创建不耗费 CPU 或磁盘的假设索引的方法。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 一个支持从数据库会话中连接到其他 PostgreSQL 数据库的模块。 有关此扩展的信息，请参阅"闪烁和postgres_fdw"部分。 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 查询和 XSLT。 |


### <a name="postgis-extensions"></a>PostGIS 扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL 的空间和地理对象。 |
> | address\_standardizer, address\_standardizer\_data\_us | 用于将地址分析成构成元素。 用于支持地理编码地址规范化步骤。 |
> | 后吉斯\_斯夫格尔 | 后GIS SFCGAL功能。 |
> | 后吉斯\_老虎\_地理编码器 | 后GIS老虎地理编码器和反向地理编码器。 |
> | 后拓扑\_拓扑 | 后GIS拓扑空间类型和函数。 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg\_stat\_语句扩展](https://www.postgresql.org/docs/current/pgstatstatements.html)在 PostgreSQL 服务器的每个 Azure 数据库上预加载，为您提供跟踪 SQL 语句的执行统计信息的方法。

该设置`pg_stat_statements.track`控制扩展计数的语句。 它默认为`top`，这意味着客户端直接发布的所有语句将被跟踪。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 作为服务器参数进行配置。

pg_stat_statements提供的查询执行信息与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果您未主动使用pg_stat_statements扩展，我们建议您设置为`pg_stat_statements.track``none`。 某些第三方监视服务可能依赖pg_stat_statements来提供查询性能见解，因此请确认是否属于您。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
您可以使用 dblink 和 postgres_fdw从一个 PostgreSQL 服务器连接到另一个服务器，或连接到同一服务器中的另一个数据库。 接收服务器需要允许来自发送服务器的连接通过其防火墙。 要使用这些扩展在 PostgreSQL 服务器的 Azure 数据库之间连接，请设置**允许对 ON 的 Azure 服务进行访问**。 如果要使用扩展循环回同一服务器，还需要打开此设置。 **"允许访问 Azure 服务**"设置可以在**连接安全**下的 Postgres 服务器的 Azure 门户页中找到。 在白名单中打开**允许访问 Azure 服务**，将列出所有 Azure IP。

目前，除了与 PostgreSQL 服务器的其他 Azure 数据库的连接外，不支持来自 PostgreSQL 的 Azure 数据库的出站连接。
