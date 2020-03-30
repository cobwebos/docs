---
title: 已知问题：从 Oracle 迁移到 Azure 数据库，用于 PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务从 Oracle 到 Azure 数据库的联机迁移的已知问题和迁移限制，以便 PostgreSQL-Single 服务器使用 PostgreSQL-Single 服务器。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235241"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>从 Oracle 联机迁移到 Azure DB for PostgreSQL（单一服务器）时存在的已知问题/迁移限制

以下部分介绍了与从 Oracle 联机迁移到 Azure Database for PostgreSQL（单一服务器）关联的已知问题和限制。

## <a name="oracle-versions-supported-as-a-source-database"></a>支持用作源数据库的 Oracle 版本

Azure 数据库迁移服务支持连接到：

- Oracle 10g、11g 和 12c 版本。
- Oracle 企业版、标准版、快速版和个人版。

Azure 数据库迁移服务不支持连接到多租户容器数据库 (CDB)。

## <a name="postgresql-versions-supported-as-a-target-database"></a>支持用作目标数据库的 PostgreSQL 版本

Azure 数据库迁移服务支持迁移到 Azure Database for PostgreSQL（单一服务器）版本 9.5、9.6、10 和 11。 请参阅[支持 PostgreSQL 数据库版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)一文，了解 Azure Database for PostgreSQL（单一服务器）中的版本支持的最新信息。

## <a name="datatype-limitations"></a>数据类型限制

以下数据类型**不会**迁移：

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

另外，空的 BLOB/CLOB 列映射到目标上的 NULL。

## <a name="lob-limitations"></a>LOB 限制

- 启用大小受限的 LOB 模式时，Oracle 源上的空 LOB 会作为 NULL 值复制。
- 不支持长的对象名称（超出 30 字节）。
- LONG 和 LONG RAW 列中的数据不能超出 64k。 将截断任何超出 64k 的数据。
- 不支持（迁移）对 LOB 列所做的任何更改（只有在 Oracle 12 中是这样）。
- 不支持（迁移）对 XMLTYPE 和 LOB 列执行的 UPDATE。

## <a name="known-issues-and-limitations"></a>已知问题和限制

- 客户必须使用 SYSDBA 连接到 Oracle。
- 因分区/子分区操作（ADD、DROP、EXCHANGE 和 TRUNCATE）而导致的数据更改不能迁移，可能导致以下错误：
  - 对于 ADD 操作，在已添加的数据上执行更新和删除操作可能会返回“0 行受影响”警告。
  - 对于 DROP 和 TRUNCATE 操作，执行新的插入操作可能会导致“重复”错误。
  - 对于 EXCHANGE 操作，“0 行受影响”和“重复”错误可能都会出现。
- 不能复制其名称包含撇号的表。
