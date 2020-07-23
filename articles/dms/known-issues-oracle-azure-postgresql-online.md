---
title: 已知问题：从 Oracle 迁移到 Azure Database for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务从 Oracle 联机迁移到 Azure Database for PostgreSQL 单服务器时存在的已知问题和迁移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 05/20/2020
ms.openlocfilehash: 2cf8ff446fe3441fc039ef3c2afef6308224666f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701216"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>从 Oracle 联机迁移到 Azure DB for PostgreSQL 单服务器时存在的已知问题/迁移限制

以下部分描述了在从 Oracle 联机迁移到 Azure Database for PostgreSQL 单服务器时存在的已知问题和限制。

## <a name="oracle-versions-supported-as-a-source-database"></a>支持用作源数据库的 Oracle 版本

Azure 数据库迁移服务支持连接到：

- Oracle 版本 10g、11g 和 12c。
- Oracle Enterprise、Standard、Express 和 Personal Edition。

Azure 数据库迁移服务不支持连接到多租户容器数据库 (CDB)。

## <a name="postgresql-versions-supported-as-a-target-database"></a>支持用作目标数据库的 PostgreSQL 版本

Azure 数据库迁移服务支持迁移到 Azure Database for PostgreSQL 单服务器版本 9.5、9.6、10 和 11。 请参阅文章[支持的 PostgreSQL 数据库版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)，以了解有关 Azure Database for PostgreSQL 单服务器中的版本支持的最新信息。

## <a name="datatype-limitations"></a>数据类型限制

以下数据类型不会迁移：

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 嵌套表
- 用户定义数据类型
- 说明
- 虚拟列
- 基于 ROWID 列的具体化视图

此外，空 BLOB/CLOB 列会映射到目标上的 NULL。

## <a name="lob-limitations"></a>LOB 限制

- 启用大小受限的 LOB 模式时，Oracle 源上的空 LOB 会作为 NULL 值复制。
- 不支持长对象名称（超过 30 字节）。
- LONG 和 LONG RAW 列中的数据不能超过 64k。 超过 64k 的所有数据都将被截断。
- 仅在 Oracle 12 中，不支持（迁移）对 LOB 列进行的任何更改。
- 不支持（迁移）对 XMLTYPE 和 LOB 列进行的 UPDATE。

## <a name="known-issues-and-limitations"></a>已知问题和限制

- 用户必须具有 Oracle 服务器上的 DBA 权限。
- 由于分区/子分区操作（ADD、DROP、EXCHANGE 和 TRUNCATE）导致的数据更改不会迁移，可能会导致以下错误：
  - 对于 ADD 操作，已添加数据上的更新和删除操作可能返回“0 行受影响”警告。
  - 对于 DROP 和 TRUNCATE 操作，新插入可能会导致“重复”错误。
  - 对于 EXCHANGE 操作，“0 行受影响”警告和“重复”错误可能都会出现。
- 名称包含撇号的表无法进行复制。
