---
title: 在最短的停机时间内迁移到 Azure Database for MySQL
description: 本文介绍如何通过使用 Attunity Replicate for Microsoft Migrations 执行在最短的停机时间内从 MySQL 数据库到 Azure Database for MySQL 的迁移，以及如何设置从源数据库到目标数据库的初始加载和持续数据同步。
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 99add55188615debdc96b6cfc8b21e34552fd9d4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267248"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>在最短的停机时间内迁移到 Azure Database for MySQL
可以使用 Attunity Replicate for Microsoft Migrations 将现有的 MySQL 数据库迁移到 Azure Database for MySQL。 Attunity Replicate 是由 Attunity 和 Microsoft 联合提供的一项服务。 它随 Azure 数据库迁移服务一起提供，Microsoft 客户无需额外付费即可拥有它。 

在数据库迁移期间，Attunity Replicate 可帮助最大限度地减少停机时间，并可使源数据库在整个过程中保持可操作。

Attunity Replicate 是一种数据复制工具，可实现不同源和目标之间的数据同步。 它可传播架构创建脚本以及与每个数据库表关联的数据。 Attunity Replicate 不会传播任何其他项目（例如 SP、触发器和函数等）或将托管在这些项目中的 PL/SQL 代码转换为 T-SQL。

> [!NOTE]
> 虽然 Attunity Replicate 支持一系列广泛的迁移方案，但它 侧重于支持源/目标对的特定子集。

执行在最短的停机时间内进行迁移的过程概述包括：

* 使用 [MySQL Workbench](https://www.mysql.com/products/workbench/)**将 MySQL 源架构迁移到** Azure Database for MySQL 托管数据库。

* **设置从源数据库到目标数据库的初始加载和持续数据同步**，方法是使用 Attunity Replicate for Microsoft Migrations。 在你准备将应用程序切换到 Azure 上的目标 MySQL 数据库时，这样做可以在最大程度上减少必须将源数据库设置为只读的时间。

有关 Attunity Replicate for Microsoft Migrations 服务的详细信息，请参阅以下资源：
 - 转到 [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) 网页。
 - 下载 [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)。
 - 转到 [Attunity Replicate 社区](https://aka.ms/attunity-community)，获取快速入门指南、教程和支持。
 - 有关使用 Attunity Replicate 将 MySQL 数据库迁移到 Azure Database for MySQL 的分布指南，请参阅[数据库迁移指南](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql)。