---
title: 优化 Azure Database for PostgreSQL 服务器中的批量插入
description: 本文介绍如何优化 Azure Database for PostgreSQL 服务器中的批量插入操作。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 2fe3c3cc71823880d71223334b89816199561ca9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628459"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>优化 Azure Database for PostgreSQL 服务器中的批量插入和瞬态数据的使用 
本文介绍如何优化 Azure Database for PostgreSQL 服务器中的批量插入操作以及瞬态数据的使用。

## <a name="using-unlogged-tables"></a>使用无日志记录表
对于其工作负荷操作涉及到瞬态数据或者需要批量插入大型数据集的客户而言，可以考虑使用无日志记录表。

无日志记录表是一项 PostgreSQL 功能，使用它能够有效地优化批量插入。 PostgreSQL 使用预写日志记录 (WAL)，此机制默认可为两个 ACID 属性提供原子性和持久性。 插入到无日志记录表意味着 PostgreSQL 会执行插入，但不会写入事务日志（此操作本身就是一项 I/O 操作），因此，这些表的访问速度比普通的表要快得多。

下面是用于创建无日志记录表的选项：
- 使用以下语法创建新的无日志记录表：`CREATE UNLOGGED TABLE <tableName>`
- 使用以下语法将现有的日志记录表转换为无日志记录表：`ALTER <tableName> SET UNLOGGED`。  可使用以下语法反转这种转换：`ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>无日志记录表的利弊
无日志记录表无法防崩溃。 发生崩溃或者执行不彻底的关机后，无日志记录表将被自动截断。 此外，无日志记录表的内容不会复制到备用服务器。 基于无日志记录表创建的任何索引也不会自动取消记录。  插入操作完成后，可将表转换为日志记录表，使插入操作具有持久性。

但是，根据我们的体验，在某些客户工作负荷中，使用无日志记录表大约可将性能改善 15-20%。

## <a name="next-steps"></a>后续步骤
查看工作负荷中使用的瞬态数据和批量插入。  

查看以下 PostgreSQL 文档 - [创建表 SQL 命令](https://www.postgresql.org/docs/current/static/sql-createtable.html)