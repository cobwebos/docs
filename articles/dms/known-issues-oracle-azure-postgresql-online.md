---
title: 已知问题：从 Oracle 迁移到 Azure Database for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务从 Oracle 在线迁移到 Azure Database for PostgreSQL 单服务器的已知问题和迁移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 0a1f46698ddb966c315d08a794dd710a74295f97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437821"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>从 Oracle 到 Azure DB for PostgreSQL 的联机迁移的已知问题/迁移限制-单服务器

以下部分介绍了与从 Oracle 到 Azure Database for PostgreSQL-单服务器的联机迁移相关的已知问题和限制。

## <a name="oracle-versions-supported-as-a-source-database"></a>支持用作源数据库的 Oracle 版本

Azure 数据库迁移服务支持连接到：

- Oracle 版本10g、11g 和12c。
- Oracle 企业版、标准版、Express 版和个人版。

Azure 数据库迁移服务不支持连接到多租户容器数据库（CDBs）。

## <a name="postgresql-versions-supported-as-a-target-database"></a>支持用作目标数据库的 PostgreSQL 版本

Azure 数据库迁移服务支持迁移到 Azure Database for PostgreSQL-单服务器版本9.5、9.6、10和11。 请参阅文章[支持的 PostgreSQL 数据库版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)，了解 Azure Database for PostgreSQL-单一服务器中的版本支持的当前信息。

## <a name="datatype-limitations"></a>数据类型限制

**不**迁移以下数据类型：

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

此外，空的 BLOB/CLOB 列映射到目标上的 NULL。

## <a name="lob-limitations"></a>LOB 限制

- 启用有限大小的 LOB 模式后，Oracle 源上的空 Lob 将复制为 NULL 值。
- 不支持长对象名称（超过30个字节）。
- 长和长原始列中的数据不能超过64k。 超过64k 的所有数据都将被截断。
- 仅在 Oracle 12 中，不支持对 LOB 列进行任何更改（迁移）。
- 不支持对 XMLTYPE 和 LOB 列的更新（已迁移）。

## <a name="known-issues-and-limitations"></a>已知问题和限制

- 客户必须使用 SYSDBA 连接到 Oracle。
- 由于分区/子分区操作（添加、删除、交换和截断）导致的数据更改不会被迁移，并可能导致以下错误：
  - 对于添加操作，添加的数据上的更新和删除可能返回 "0 行受影响" 警告。
  - 对于 DROP 和截断操作，新插入可能会导致 "重复" 错误。
  - 对于 EXCHANGE 操作，可能会出现 "0 行受影响" 警告和 "重复" 错误。
- 名称包含撇号的表无法进行复制。
