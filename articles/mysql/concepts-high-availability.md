---
title: 高可用性-Azure Database for MySQL
description: 本文提供了有关 Azure Database for MySQL 中的高可用性的信息
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: bd2f7798ca02f4d6eab6d6d78d158a48bcccc010
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206078"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的高可用性
Azure Database for MySQL 服务提供有保证的高级别可用性，同时提供[99.99%](https://azure.microsoft.com/support/legal/sla/mysql)运行时间 (SLA) 的财务支持服务级别协议。 Azure Database for MySQL 在计划事件（如用户发起的缩放计算操作）期间提供高可用性，并且还在发生基础硬件、软件或网络故障等计划外事件时提供高可用性。 Azure Database for MySQL 可以从大多数关键情况快速恢复，确保在使用此服务时几乎不会关闭应用程序。

Azure Database for MySQL 适用于运行需要高运行时间的任务关键型数据库。 该服务在 Azure 体系结构的基础上构建，具有固有的高可用性、冗余和复原能力，可减少计划内和计划外停机的数据库停机时间，而无需配置任何其他组件。 

## <a name="components-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的组件

| **组件** | **说明**|
| ------------ | ----------- |
| <b>MySQL 数据库服务器 | Azure Database for MySQL 为数据库服务器提供安全、隔离、资源保护和快速重新启动功能。 这些功能有助于在几秒钟内发生中断后执行缩放和数据库服务器恢复操作等操作。 <br/> 数据库服务器中的数据修改通常发生在数据库事务的上下文中。 所有数据库更改都以 "预写入日志" 的形式以同步方式记录 (ib_log Azure 存储上的) （附加到数据库服务器）。 在数据库[检查点](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html)过程中，数据库服务器内存中的数据页也会刷新到存储中。 |
| <b>远程存储 | 所有 MySQL 物理数据文件和日志文件都存储在 Azure 存储中，这种存储可用于在一个区域内存储三个数据副本，以确保数据冗余、可用性和可靠性。 存储层还独立于数据库服务器。 它可以从失败的数据库服务器分离，并在几秒钟内重新附加到新的数据库服务器。 此外，Azure 存储会持续监视是否存在任何存储故障。 如果检测到块损坏，则会通过实例化新的存储副本来自动修复。 |
| <b>网关 | 网关充当数据库代理，将所有客户端连接路由到数据库服务器。 |

## <a name="planned-downtime-mitigation"></a>计划内停机缓解
Azure Database for MySQL 旨在在计划的停机时间内提供高可用性。 

![Azure MySQL 中的弹性缩放视图](./media/concepts-high-availability/elastic-scaling-mysql-server.png)

下面是一些计划内维护方案：

| **方案** | 说明|
| ------------ | ----------- |
| <b>计算扩展/缩减 | 当用户执行计算增加/减少操作时，将使用缩放的计算配置来预配新的数据库服务器。 在旧的数据库服务器中，允许完成活动检查点，客户端连接将耗尽，所有未提交的事务将被取消，然后关闭。 然后，将从旧数据库服务器分离存储并将其附加到新的数据库服务器。 当客户端应用程序重试连接或尝试建立新连接时，网关会将连接请求定向到新的数据库服务器。|
| <b>扩展存储 | 扩展存储是一种联机操作，不会中断数据库服务器。|
| <b>Azure)  (新的软件部署 | 新功能的推出或 bug 修复会自动在服务的计划内维护过程中发生。 有关详细信息，请参阅[文档](concepts-monitoring.md#planned-maintenance-notification)，并查看[门户](https://aka.ms/servicehealthpm)。|
| <b>次版本升级 | Azure Database for MySQL 会自动将数据库服务器修补到 Azure 确定的次要版本。 它在服务的计划内维护过程中发生。 这会导致短暂的停机时间（以秒为单位），并且会使用新的次要版本自动重新启动数据库服务器。 有关详细信息，请参阅[文档](concepts-monitoring.md#planned-maintenance-notification)，并查看[门户](https://aka.ms/servicehealthpm)。|


##  <a name="unplanned-downtime-mitigation"></a>计划外停机缓解

由于不可预见的故障（包括底层硬件故障、网络问题和软件错误）导致不计划的停机时间。 如果数据库服务器意外中断，则会在数秒内自动设置新的数据库服务器。 远程存储会自动连接到新的数据库服务器。 MySQL 引擎使用 WAL 和数据库文件执行恢复操作，并打开数据库服务器以允许客户端连接。 未提交的事务将丢失，并且必须由应用程序重试。 尽管不能避免非计划的停机时间，但 Azure Database for MySQL 可以通过在数据库服务器和存储层上自动执行恢复操作而无需人工干预来减少停机时间。 


![Azure MySQL 中的高可用性视图](./media/concepts-high-availability/availability-for-mysql-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>计划外停机：故障方案和服务恢复
下面是一些故障方案以及 Azure Database for MySQL 如何自动恢复：

| **方案** | **自动恢复** |
| ---------- | ---------- |
| <B>数据库服务器故障 | 如果数据库服务器由于某些基础硬件故障而关闭，则会丢弃活动的连接，并中止所有即时事务。 将自动部署新的数据库服务器，并将远程数据存储附加到新的数据库服务器。 数据库恢复完成后，客户端可以通过网关连接到新的数据库服务器。 <br /> <br /> 使用 MySQL 数据库的应用程序需要以检测和重试已删除的连接和失败的事务的方式进行构建。  当应用程序重试时，网关会将连接透明地重定向到新创建的数据库服务器。 |
| <B>存储失败 | 应用程序不会对与存储相关的任何问题（例如磁盘故障或物理块损坏）产生任何影响。 当数据存储在3个副本中时，数据的副本将由幸存下来的存储提供服务。 自动更正损坏。 如果丢失了数据的副本，则会自动创建数据的新副本。 |

下面是一些要求用户操作恢复的故障方案：

| **方案** | **恢复计划** |
| ---------- | ---------- |
| <b>区域故障 | 如果某个区域发生故障，这种情况很少发生。 但是，如果需要从区域故障中获得保护，则可以在其他区域中配置一个或多个读取副本，以便进行灾难恢复 (DR) 。  (参阅[此文](howto-read-replicas-portal.md)，了解有关详细信息) 有关创建和管理读取副本的信息。 如果出现区域级故障，可以手动将其他区域上配置的读取副本提升为生产数据库服务器。 |
| <b>逻辑/用户错误 | 从用户错误（例如意外删除的表或错误更新的数据）进行的恢复涉及到执行[时间点恢复](concepts-backup.md) (PITR) ，方法是在错误发生之前还原和恢复数据。<br> <br>  如果要仅还原数据库或特定表的一个子集，而不是数据库服务器中的所有数据库，可以在新的实例中还原数据库服务器，通过[mysqldump](concepts-migrate-dump-restore.md)将表导出 () ，然后使用[restore](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench)将这些表还原到数据库。 |



## <a name="summary"></a>总结

Azure Database for MySQL 提供了数据库服务器、冗余存储和网关的高效路由的快速重新启动功能。 对于其他数据保护，你可以将备份配置为异地复制，同时在其他区域部署一个或多个读取副本。 利用固有的高可用性功能，Azure Database for MySQL 保护数据库免受最常见的服务中断的影响，并提供行业领先、支持融资的[99.99% 的运行时间 SLA](https://azure.microsoft.com/support/legal/sla/mysql)。 所有这些可用性和可靠性功能使得 Azure 成为运行任务关键型应用程序的理想平台。

## <a name="next-steps"></a>后续步骤
- 了解[Azure 区域](../availability-zones/az-overview.md)
- 了解如何[处理暂时性连接错误](concepts-connectivity.md)
- 了解如何[使用只读副本复制数据](howto-read-replicas-portal.md)