---
title: 使用 Azure Database for PostgreSQL（单一服务器）中的 PostgreSQL 扩展
description: 介绍有关使用 Azure Database for PostgreSQL（单一服务器）中的扩展来扩展数据库功能的功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 4f81b23378427faa522071f4a20e07485f5c3387
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296412"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的 PostgreSQL 扩展
PostgreSQL 支持使用扩展来扩展数据的功能。 扩展在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载之后，扩展会如同内置功能一样运行。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 扩展
必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请通过 psql 工具运行  [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html)  命令，将打包的对象加载到数据库中。

用于 PostgreSQL 的 Azure 数据库支持下面列出的一部分键扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。 不支持未列出的其他扩展。 不能在 Azure Database for PostgreSQL 中创建自己的扩展。

## <a name="postgres-11-extensions"></a>Postgres 11 扩展

以下扩展在 Azure Database for PostgreSQL 服务器中提供，此类服务器的 Postgres 版本为 11。 

> [!div class="mx-tableFixed"]
> | **扩展**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 数据集示例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支持在 GiST 中索引常见数据类型|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用于存储（键/值）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | 用于 PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 用于国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 用于分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 函数和运算符，用于模拟商业 RDBMS 提供的部分函数和包|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 一种扩展，用于按时间或 ID 管理已分区表|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 基于三元匹配的文本相似度度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) 信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS 几何结构、地理以及光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 可操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 允许对时序数据进行可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|

## <a name="postgres-10-extensions"></a>Postgres 10 扩展 

以下扩展在 Azure Database for PostgreSQL 服务器中提供，此类服务器的 Postgres 版本为 10。

> [!div class="mx-tableFixed"]
> | **扩展**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 数据集示例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 支持在 GiST 中索引常见数据类型|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 用于自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 用于存储（键/值）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 用于 PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 用于国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 用于分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 函数和运算符，用于模拟商业 RDBMS 提供的部分函数和包|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 一种扩展，用于按时间或 ID 管理已分区表|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 基于三元匹配的文本相似度度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS 几何结构、地理以及光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 可操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 允许对时序数据进行可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|

## <a name="postgres-96-extensions"></a>Postgres 9.6 扩展 

以下扩展在 Azure Database for PostgreSQL 服务器中提供，此类服务器的 Postgres 版本为 9.6。

> [!div class="mx-tableFixed"]
> | **扩展**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US 数据集示例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 支持在 GiST 中索引常见数据类型|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 用于自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 用于存储（键/值）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 用于 PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 用于国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 用于分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 函数和运算符，用于模拟商业 RDBMS 提供的部分函数和包|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 一种扩展，用于按时间或 ID 管理已分区表|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 基于三元匹配的文本相似度度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS 几何结构、地理以及光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 可操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 允许对时序数据进行可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (UUID)|

## <a name="postgres-95-extensions"></a>Postgres 9.5 扩展 

以下扩展在 Azure Database for PostgreSQL 服务器中提供，此类服务器的 Postgres 版本为 9.5。

> [!div class="mx-tableFixed"]
> | **扩展**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US 数据集示例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | 支持在 GIN 中索引常见数据类型|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 支持在 GiST 中索引常见数据类型|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 用于自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 用于多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 从数据库中连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 用于整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 确定字符串间的相似性和差异|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 用于存储（键/值）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 用于 PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 针对 1-D 整数数组的函数、运算符和索引支持|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 用于国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 用于分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 函数和运算符，用于模拟商业 RDBMS 提供的部分函数和包|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 一种扩展，用于按时间或 ID 管理已分区表|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 跟踪已执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 基于三元匹配的文本相似度度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS 几何结构、地理以及光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger 地理编码器和逆向地理编码器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 外部数据包装器，用于远程 PostgreSQL 服务器|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 可操作整个表（包括交叉表）的函数|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 删除了重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | 生成全局唯一标识符 (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
pg_stat_statements 扩展预加载在每个 Azure Database for PostgreSQL 服务器上，以便跟踪 SQL 语句的执行统计信息。
设置 `pg_stat_statements.track`，它可以控制哪些语句由扩展计数，默认为 `top`，这意味着跟踪所有由客户端直接发布的语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 作为服务器参数进行配置。

查询执行信息 pg_stat_statements 提供的权限与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果不经常使用 pg_stat_statements 扩展，则建议将 `pg_stat_statements.track` 设置为 `none`。 请注意，某些第三方监视服务可能依赖 pg_stat_statements 来提供查询性能见解，因此，请确认这是否适合你。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
dblink 和 postgres_fdw 允许从一台 PostgreSQL 服务器连接到另一台服务器，或者连接到同一服务器中的另一个数据库。 接收服务器需要允许来自发送服务器的连接通过其防火墙。 当使用这些扩展在 Azure Database for PostgreSQL 服务器之间进行连接时，可以通过将“允许访问 Azure 服务”设置为“开启”来实现此目的。 如果希望使用扩展来环回到同一服务器，也需要进行此设置。 可以在 Postgres 服务器的 Azure 门户页面中的“连接安全性”下找到“允许访问 Azure 服务”设置。 开启“允许访问 Azure 服务”会将所有 Azure IP 置于允许列表中。

目前不支持从 Azure Database for PostgreSQL 进行出站连接，连接到其他 Azure Database for PostgreSQL 服务器的情况除外。

## <a name="uuid"></a>uuid
如果计划使用 uuid-ossp 扩展中的 `uuid_generate_v4()`，请考虑将其与 pgcrypto 扩展中的 `gen_random_uuid()` 进行比较，以了解性能优势。


## <a name="pgaudit"></a>pgAudit
PgAudit 扩展提供会话和对象审核日志记录。 若要了解如何在 Azure Database for PostgreSQL 中使用此扩展，请访问[审核概念一文](concepts-audit.md)。 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一个时序数据库，已作为 PostgreSQL 的扩展打包。 TimescaleDB 提供以时间为导向的分析功能、优化，并根据时序工作负荷来缩放 Postgres。

[详细了解 TimescaleDB](https://docs.timescale.com/latest)，[Timescale, Inc.](https://www.timescale.com/) 的注册商标。

### <a name="installing-timescaledb"></a>安装 TimescaleDB
若要安装 TimescaleDB，需将其包括在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要**重启服务器**才能生效。 可以使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 更改参数。

使用 [Azure 门户](https://portal.azure.com/)：

1. 选择你的 Azure Database for PostgreSQL 服务器。

2. 在侧栏中选择“服务器参数”。

3. 搜索 `shared_preload_libraries` 参数。

4. 选择“TimescaleDB”。

5. 选择“保存”，保留所做的更改。 保存更改后会获得通知。 

6. 获得通知后，请**重启**服务器以应用这些更改。 若要了解如何重启服务器，请参阅[重启 Azure Database for PostgreSQL 服务器](howto-restart-server-portal.md)。


现在可以在 Postgres 数据库中启用 TimescaleDB。 连接到数据库并发出以下命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果看到错误，请确认是否已在保存 shared_preload_libraries 后[重启服务器](howto-restart-server-portal.md)。 

现在可以[从头开始](https://docs.timescale.com/getting-started/creating-hypertables)创建 TimescaleDB hypertable，也可以迁移 [PostgreSQL 中的现有时序数据](https://docs.timescale.com/getting-started/migrating-data)。


## <a name="next-steps"></a>后续步骤
如果未看到要使用的扩展，请告诉我们。 在我们的[反馈论坛](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票现有请求或创建新的反馈请求。
