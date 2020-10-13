---
title: 高可用性 - Azure Database for MySQL
description: 本文提供了有关 Azure Database for MySQL 中的高可用性的信息
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 668243f66deff67a923097c116c4b150d0256992
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90882555"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的高可用性
Azure Database for MySQL 服务提供有保证的高级别可用性，即，提供正常运行时间占比为 [99.99%](https://azure.microsoft.com/support/legal/sla/mysql) 且具有财务支持的服务级别协议 (SLA)。 Azure Database for MySQL 在发生计划内事件（例如用户发起的缩放计算操作）期间提供高可用性，并且还在发生基础硬件、软件或网络故障等计划外事件时提供高可用性。 Azure Database for MySQL 在发生大多数严重状况时都可以快速恢复，确保用户在使用此服务时应用程序几乎不会停机。

Azure Database for MySQL 适合运行对正常运行时间要求很高的关键数据库。 该服务基于 Azure 体系结构构建，具有固有的高可用性、冗余性和复原能力，可以缓解计划内和计划外中断造成的数据库停机，不需要你配置任何其他组件。 

## <a name="components-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的组件

| **组件** | **说明**|
| ------------ | ----------- |
| <b>MySQL 数据库服务器 | Azure Database for MySQL 为数据库服务器提供安全性、隔离、资源保护和快速重启功能。 这些功能有助于在发生中断后的几秒钟内执行缩放操作和数据库服务器恢复操作等操作。 <br/> 数据库服务器中的数据修改通常发生在数据库事务的上下文中。 所有数据库更改都以预写日志 (ib_log) 的形式同步记录在 Azure 存储上，该存储附加到数据库服务器。 在数据库[检查点](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html)过程中，数据库服务器内存中的数据页也会刷新到存储中。 |
| <b>远程存储 | 所有 MySQL 物理数据文件和日志文件都存储在 Azure 存储中，该存储设计为在一个区域中存储数据的三个副本，以确保数据冗余、可用性和可靠性。 存储层还独立于数据库服务器。 它可以在几秒内从发生故障的数据库服务器分离并重新附加到新的数据库服务器。 此外，Azure 存储还会持续监视是否存在任何存储故障。 如果检测到块损坏，则会通过实例化新的存储副本来自动修复。 |
| <b>网关 | 网关充当数据库代理，将所有客户端连接路由到数据库服务器。 |

## <a name="planned-downtime-mitigation"></a>缓解计划内停机
Azure Database for MySQL 设计为在计划内停机操作期间提供高可用性。 

:::image type="content" source="./media/concepts-high-availability/elastic-scaling-mysql-server.png" alt-text="Azure MySQL 中的弹性缩放的视图":::

下面是一些计划内维护方案：

| **方案** | **说明**|
| ------------ | ----------- |
| <b>计算纵向扩展/缩减 | 当用户执行计算纵向扩展/缩减操作时，将使用缩放的计算配置来预配新的数据库服务器。 在旧的数据库服务器中，将允许处于活动状态的检查点完成，客户端连接将排空，所有未提交的事务将取消，然后将关闭该服务器。 然后会从旧数据库服务器分离存储并将其附加到新的数据库服务器。 当客户端应用程序重试连接或尝试建立新连接时，网关会将连接请求定向到新的数据库服务器。|
| <b>纵向扩展存储 | 纵向扩展存储是一种联机操作，不会中断数据库服务器。|
| <b>新软件部署 (Azure) | 新功能的推出或 bug 修复会自动在服务的计划内维护过程中发生。 有关详细信息，请参阅[文档](concepts-monitoring.md#planned-maintenance-notification)并检查你的[门户](https://aka.ms/servicehealthpm)。|
| <b>次要版本升级 | Azure Database for MySQL 会自动将数据库服务器修补到 Azure 确定的次要版本。 这是在服务的计划内维护过程中发生的。 这会导致短暂的停机（以秒为单位），并且会自动重启装有新次要版本的数据库服务器。 有关详细信息，请参阅[文档](concepts-monitoring.md#planned-maintenance-notification)并检查你的[门户](https://aka.ms/servicehealthpm)。|


##  <a name="unplanned-downtime-mitigation"></a>缓解计划外停机

意外的故障（包括基础硬件故障、网络问题和软件 bug）可能会导致计划外停机。 如果数据库服务器意外关闭，则会在数秒内自动预配一个新的数据库服务器。 远程存储会自动附加到新的数据库服务器。 MySQL 引擎使用 WAL 和数据库文件执行恢复操作，并打开数据库服务器以允许客户端进行连接。 未提交的事务将丢失，并且必须由应用程序重试。 虽然计划外停机无法避免，但 Azure Database for MySQL 可以通过在数据库服务器和存储层上自动执行恢复操作来减少停机时间，无需人工干预。 


:::image type="content" source="./media/concepts-high-availability/availability-for-mysql-server.png" alt-text="Azure MySQL 中的弹性缩放的视图":::

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>计划外停机：故障场景和服务恢复
下面介绍了一些故障场景以及 Azure Database for MySQL 如何自动恢复：

| **方案** | **自动恢复** |
| ---------- | ---------- |
| <B>数据库服务器故障 | 如果数据库服务器由于某些基础硬件故障而关闭，则会丢弃处于活动状态的连接，并中止任何正在进行的事务。 将自动部署新的数据库服务器，并将远程数据存储附加到新的数据库服务器。 在数据库恢复完成后，客户端可以通过网关连接到新的数据库服务器。 <br /> <br /> 所构建的使用 MySQL 数据库的应用程序需要能够检测并重试丢弃的连接和失败的事务。  当应用程序重试时，网关会将连接透明地重定向到新创建的数据库服务器。 |
| <B>存储故障 | 对于任何与存储相关的问题（例如磁盘故障或物理块损坏），应用程序看不到任何影响。 由于数据存储在 3 个副本中，因此将由未发生故障的存储提供数据的副本。 块损坏会自动修复。 如果丢失了数据的副本，则会自动创建数据的新副本。 |

下面是需要用户执行操作来进行恢复的一些故障场景：

| **方案** | **恢复计划** |
| ---------- | ---------- |
| <b> 区域故障 | 区域故障非常少见。 但是，如果需要在发生区域故障时获得保护，则可在其他区域中配置一个或多个用于灾难恢复 (DR) 的只读副本。 （请参阅[此文](howto-read-replicas-portal.md)，详细了解如何创建和管理只读副本）。 如果出现区域级故障，可以手动将其他区域上配置的只读副本提升为生产数据库服务器。 |
| <b> 逻辑/用户错误 | 在发生用户错误（例如，意外删除了表或错误地更新了数据）后进行的恢复涉及到执行[时间点恢复](concepts-backup.md) (PITR)，方法是将数据还原并恢复到发生错误之前的那个时间点。<br> <br>  如果只需还原部分数据库或特定的表，而不是还原数据库服务器中的所有数据库，则可在新实例中还原数据库服务器，通过 [mysqldump](concepts-migrate-dump-restore.md) 导出表，然后使用 [restore](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench) 将这些表还原到数据库中。 |



## <a name="summary"></a>摘要

Azure Database for MySQL 提供了数据库服务器快速重启功能、冗余存储和网关的高效路由。 为了进一步进行数据保护，你可以将备份配置为异地复制的备份，同时在其他区域中部署一个或多个只读副本。 利用固有的高可用性功能，Azure Database for MySQL 保护数据库免受最常见的服务中断影响，并提供行业领先且具有财务支持的[正常运行时间占比为 99.99% 的 SLA](https://azure.microsoft.com/support/legal/sla/mysql)。 所有这些可用性和可靠性功能使得 Azure 成为运行关键应用程序的理想平台。

## <a name="next-steps"></a>后续步骤
- 了解 [Azure 区域](../availability-zones/az-overview.md)
- 了解如何[处理暂时性连接错误](concepts-connectivity.md)
- 了解如何[使用只读副本复制数据](howto-read-replicas-portal.md)