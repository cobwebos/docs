---
title: "在最短的停机时间内迁移到 Azure Database for PostgreSQL | Microsoft Docs"
description: "本文介绍如何通过将 PostgreSQL 数据库提取到转储文件，从由 Azure Database for PostgreSQL 中的 pg_dump 创建的存档文件还原 PostgreSQL 数据库，并使用 Attunity Replicate for Microsoft Migrations 设置从源数据库到目标数据库的初始加载和持续数据同步，以执行在最短的故障时间内进行的迁移。"
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: efbd4f227880875c11e2c43c84716dfc49c5717d
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>在最短的停机时间内迁移到 Azure Database for PostgreSQL
可以使用 Attunity Replicate for Microsoft Migrations 将现有的 PostgreSQL 数据库迁移到 Azure Database for PostgreSQL。 Attunity Replicate 是由 Attunity 和 Microsoft 联合提供的一项服务。 它随 Azure 数据库迁移服务一起提供，Microsoft 客户无需额外付费即可拥有它。 

在数据库迁移期间，Attunity Replicate 可帮助最大限度地减少停机时间，并可使源数据库在整个过程中保持可操作。

Attunity Replicate 是一种数据复制工具，可实现不同源和目标之间的数据同步。 它可传播架构创建脚本以及与每个数据库表关联的数据。 Attunity Replicate 不会传播任何其他项目（例如 SP、触发器和函数等）或将托管在这些项目中的 PL/SQL 代码转换为 T-SQL。

> [!NOTE]
> 虽然 Attunity Replicate 支持一系列广泛的迁移方案，但它 侧重于支持源/目标对的特定子集。

执行在最短的停机时间内进行迁移的过程概述包括：

* **将 PostgreSQL 源架构迁移到** Azure Database for PostgreSQL 数据库，方法是使用具有 -n 参数的 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 命令，然后使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 命令。

* **设置从源数据库到目标数据库的初始加载和持续数据同步**，方法是使用 Attunity Replicate for Microsoft Migrations。 在你准备将应用程序切换到 Azure 上的目标 PostgreSQL 数据库时，这样做可以在最大程度上减少必须将源数据库设置为只读的时间。

有关 Attunity Replicate for Microsoft Migrations 服务的详细信息，请参阅以下资源：
 - 转到 [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) 网页。
 - 下载 [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)。
 - 转到 [Attunity Replicate 社区](https://aka.ms/attunity-community/)，获取快速入门指南、教程和支持。
 - 有关使用 Attunity Replicate 从 PostgreSQL 迁移到 Azure Database for PostgreSQL 的分布指南，请参阅[数据库迁移指南](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql)。