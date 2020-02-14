---
title: 扩展-Azure Database for PostgreSQL-单个服务器
description: 了解 Azure Database for PostgreSQL-单服务器中可用的 Postgres 扩展
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201265"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 中的 PostgreSQL 扩展-单服务器
PostgreSQL 支持使用扩展来扩展数据的功能。 扩展将多个相关的 SQL 对象组合在一起，可以使用单个命令在数据库中加载或删除单个包。 在数据库中加载后，扩展功能类似于内置功能。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 扩展
必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请通过 psql 工具运行  [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html)  命令，将打包的对象加载到数据库中。

Azure Database for PostgreSQL 支持下面列出的一小部分关键扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。 不支持超出列出的扩展名。 你无法在 Azure Database for PostgreSQL 中创建你自己的扩展。

## <a name="postgres-11-extensions"></a>Postgres 11 扩展

以下扩展在 Postgres 版本为11的 Azure Database for PostgreSQL 服务器中可用。 

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US dataset 示例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 对 GIN 中常见数据类型的索引的支持|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 对 GiST 中常见数据类型的索引的支持|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 从数据库内连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 确定两个字符串之间的相似性和距离|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用于存储（key，value）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 对一个二维整数数组的函数、运算符和索引的支持|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 用于从商业 RDBMS 模拟函数和包子集的函数和运算符|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 用于按时间或 ID 管理已分区表的扩展|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 跟踪执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 基于 trigrams 的文本相似性度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript （v8）受信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder and reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 远程 PostgreSQL 服务器的外部数据包装器|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 为时序数据启用可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 删除重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符（Uuid）|

## <a name="postgres-10-extensions"></a>Postgres 10 扩展 

具有 Postgres 版本10的 Azure Database for PostgreSQL 服务器中提供以下扩展。

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US dataset 示例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | 对 GIN 中常见数据类型的索引的支持|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 对 GiST 中常见数据类型的索引的支持|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 从数据库内连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 确定两个字符串之间的相似性和距离|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 用于存储（key，value）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 对一个二维整数数组的函数、运算符和索引的支持|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 用于从商业 RDBMS 模拟函数和包子集的函数和运算符|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 用于按时间或 ID 管理已分区表的扩展|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 跟踪执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 基于 trigrams 的文本相似性度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8）受信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometry、geography 和光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger geocoder and reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 远程 PostgreSQL 服务器的外部数据包装器|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 为时序数据启用可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 删除重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符（Uuid）|

## <a name="postgres-96-extensions"></a>Postgres 9.6 扩展 

具有 Postgres 版本9.6 的 Azure Database for PostgreSQL 服务器中提供了以下扩展。

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US dataset 示例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | 对 GIN 中常见数据类型的索引的支持|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 对 GiST 中常见数据类型的索引的支持|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 从数据库内连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 确定两个字符串之间的相似性和距离|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 用于存储（key，value）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 对一个二维整数数组的函数、运算符和索引的支持|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 用于从商业 RDBMS 模拟函数和包子集的函数和运算符|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 用于按时间或 ID 管理已分区表的扩展|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 跟踪执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 基于 trigrams 的文本相似性度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8）受信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometry、geography 和光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger geocoder and reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 远程 PostgreSQL 服务器的外部数据包装器|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 操作整个表（包括交叉表）的函数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 为时序数据启用可缩放的插入和复杂查询|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 删除重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符（Uuid）|

## <a name="postgres-95-extensions"></a>Postgres 9.5 扩展 

具有 Postgres 版本9.5 的 Azure Database for PostgreSQL 服务器中提供了以下扩展。

> [!div class="mx-tableFixed"]
> | **扩展名**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US dataset 示例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | 对 GIN 中常见数据类型的索引的支持|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 对 GiST 中常见数据类型的索引的支持|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 自动加密密码的数据类型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 从数据库内连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 确定两个字符串之间的相似性和距离|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 用于存储（key，value）对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 对一个二维整数数组的函数、运算符和索引的支持|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 用于从商业 RDBMS 模拟函数和包子集的函数和运算符|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 提供审核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 用于按时间或 ID 管理已分区表的扩展|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 跟踪执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 基于 trigrams 的文本相似性度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometry、geography 和光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger geocoder and reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 远程 PostgreSQL 服务器的外部数据包装器|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 操作整个表（包括交叉表）的函数|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 删除重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | 生成全局唯一标识符（Uuid）|


## <a name="pg_stat_statements"></a>pg_stat_statements
在每个 Azure Database for PostgreSQL 服务器上预加载[pg_stat_statements 扩展](https://www.postgresql.org/docs/current/pgstatstatements.html)，以便为你提供一种跟踪 SQL 语句执行统计信息的方法。
设置 `pg_stat_statements.track`，它可以控制哪些语句由扩展计数，默认为 `top`，这意味着跟踪所有由客户端直接发布的语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 作为服务器参数进行配置。

查询执行信息 pg_stat_statements 提供的权限与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果不经常使用 pg_stat_statements 扩展，则建议将 `pg_stat_statements.track` 设置为 `none`。 请注意，某些第三方监视服务可能依赖 pg_stat_statements 来提供查询性能见解，因此，请确认这是否适合你。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html)和[postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html)允许从一台 PostgreSQL 服务器连接到另一台服务器，或连接到同一服务器上的其他数据库。 接收服务器需要允许来自发送服务器的连接通过其防火墙。 当使用这些扩展在 Azure Database for PostgreSQL 服务器之间进行连接时，可以通过将“允许访问 Azure 服务”设置为“开启”来实现此目的。 如果希望使用扩展来环回到同一服务器，也需要进行此设置。 可以在 Postgres 服务器的 Azure 门户页面中的“连接安全性”下找到“允许访问 Azure 服务”设置。 启用 "允许访问 Azure 服务" 时，将所有 Azure Ip 置于允许列表中。

目前，不支持来自 Azure Database for PostgreSQL 的出站连接，但与其他 Azure Database for PostgreSQL 服务器的连接除外。

## <a name="uuid"></a>uuid
如果计划使用[ossp 扩展](https://www.postgresql.org/docs/current/uuid-ossp.html)中的 `uuid_generate_v4()`，请考虑将与[pgcrypto 扩展](https://www.postgresql.org/docs/current/pgcrypto.html)中的 `gen_random_uuid()` 进行比较以获得性能优势。

## <a name="pgaudit"></a>pgAudit
[PgAudit 扩展](https://github.com/pgaudit/pgaudit/blob/master/README.md)提供会话和对象审核日志记录。 若要了解如何在 Azure Database for PostgreSQL 中使用此扩展，请访问[审核概念一文](concepts-audit.md)。 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm 扩展将关系数据加载到缓存中。 Prewarming 缓存意味着在重启后，查询首次运行时的响应时间更好。 在 Postgres 10 和更低的 prewarming 中，使用[prewarm 函数](https://www.postgresql.org/docs/10/pgprewarm.html)手动完成了。

在 Postgres 11 及更高版本中，你可以将 prewarming 配置为[自动](https://www.postgresql.org/docs/current/pgprewarm.html)发生。 需要将 pg_prewarm 包含在 `shared_preload_libraries` 参数的列表中，然后重新启动服务器以应用更改。 可以从[Azure 门户](howto-configure-server-parameters-using-portal.md)、 [CLI](howto-configure-server-parameters-using-cli.md)、REST API 或 ARM 模板设置参数。 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一个作为 PostgreSQL 的扩展打包的时间系列数据库。 TimescaleDB 提供面向时间的分析函数、优化，并为时序工作负荷扩展 Postgres。

[了解有关 TimescaleDB 的详细信息](https://docs.timescale.com/latest)，即[时间刻度](https://www.timescale.com/)的注册商标。 Azure Database for PostgreSQL 提供时间刻度的开源版本。 若要了解此版本中有哪些时间刻度功能可用，请参阅[时间刻度产品比较](https://www.timescale.com/products/)。

### <a name="installing-timescaledb"></a>安装 TimescaleDB
若要安装 TimescaleDB，需要将其包含在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要**服务器重启**才能生效。 您可以使用[Azure 门户](howto-configure-server-parameters-using-portal.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)来更改参数。

使用[Azure 门户](https://portal.azure.com/)：

1. 选择你的 Azure Database for PostgreSQL 服务器。

2. 在边栏中选择 "**服务器参数**"。

3. 搜索 `shared_preload_libraries` 参数。

4. 选择**TimescaleDB**。

5. 选择 "**保存" 保存**所做的更改。 保存更改后，会收到通知。 

6. 通知完成后，**重新启动**服务器以应用这些更改。 若要了解如何重启服务器，请参阅[重启 Azure Database for PostgreSQL 服务器](howto-restart-server-portal.md)。


你现在可以在 Postgres 数据库中启用 TimescaleDB。 连接到数据库并发出以下命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果出现错误，请确认在保存后[重新启动了服务器](howto-restart-server-portal.md)shared_preload_libraries。 

现在，你可以[从头](https://docs.timescale.com/getting-started/creating-hypertables)开始创建 TimescaleDB hypertable 或迁移[PostgreSQL 中现有的时序数据](https://docs.timescale.com/getting-started/migrating-data)。

### <a name="restoring-a-timescale-database"></a>还原时间刻度数据库
若要使用 pg_dump 和 pg_restore 还原时间刻度数据库，需要在目标数据库中运行两个帮助器过程： `timescaledb_pre_restore()` 和 `timescaledb_post restore()`。

首先准备目标数据库：

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

现在，可以在原始数据库上运行 pg_dump，然后 pg_restore。 还原后，请确保在还原的数据库中运行以下命令：

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>后续步骤
如果未看到要使用的扩展，请告诉我们。 在我们的[反馈论坛](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票现有请求或创建新的反馈请求。
