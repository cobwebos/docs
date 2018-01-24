---
title: "在最短的故障时间内迁移到 Azure Database for MySQL | Microsoft Docs"
description: "本文介绍如何通过使用 Attunity Replicate for Microsoft Migrations 执行在最短的故障时间内从 MySQL 数据库到 Azure Database for MySQL 的迁移，以及如何设置从源数据库到目标数据库的初始加载和持续数据同步。"
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>在最短的故障时间内迁移到 Azure Database for MySQL
可以使用 Attunity Replicate for Microsoft Migrations 将现有的 MySQL 数据库迁移到 Azure Database for MySQL，Attunity Replicate for Microsoft Migrations 是由 Attunity 和 Microsoft 共同赞助、联合提供的一项服务，随 Azure 数据库迁移服务一起提供，不会对 Microsoft 客户造成任何额外的费用。 Attunity Replicate for Microsoft Migrations 还支持在最短的故障时间内进行数据库迁移，以及在迁移过程期间可继续操作源数据库。

Attunity Replicate 是一种数据复制工具，支持在大量源和目标之间进行数据同步，传播架构创建脚本和与每个数据库表关联的数据。 Attunity Replicate 不会传播任何其他项目（例如 SP、触发器和函数等）或将托管在这些项目中的 PL/SQL 代码转换到 T-SQL。

> [!NOTE]
> Attunity Replicate 支持一系列广泛的迁移方案，而 Attunity Replicate for Microsoft Migrations 侧重于对源/目标对的特定子集的支持。

执行在最短的故障时间内进行迁移的过程概述包括：

1. **将 MySQL 源架构迁移到** Azure Database for MySQL 数据库，方法是使用 [MySQL Workbench](https://www.mysql.com/products/workbench/)。

2. **设置从源数据库到目标数据库的初始加载和持续数据同步**，方法是使用 Attunity Replicate for Microsoft Migrations。 在你准备将应用程序切换到 Azure 上的目标 MySQL 数据库时，这样做可以在最大程度上减少必须将源数据库设置为只读的时间。

有关 Attunity Replicate for Microsoft Migrations 服务的详细信息，请查看以下资源：
 - Attunity Replicate for Microsoft Migrations [网页](https://aka.ms/attunity-replicate)。
 - [下载](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity Replicate for Microsoft Migrations。
 - 提供快速入门指南、教程和支持的 Attunity Replicate [社区](https://microsoft.attunity.com/)。
 - 有关使用 Attunity 从 MySQL 迁移到 Azure Database for MySQL 的分布指南，请参阅[数据库迁移指南](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql)。