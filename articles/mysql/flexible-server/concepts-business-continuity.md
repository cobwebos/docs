---
title: Azure Database for MySQL 灵活的服务器的业务连续性概述
description: 了解 Azure Database for MySQL 灵活服务器的业务连续性概念
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6beab6f470a39c281020bfdfb7d43c4b6c5e3b70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91756494"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版的业务连续性概述) 

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

Azure Database for MySQL 灵活的服务器可实现业务连续性功能，以便在发生计划内和计划外服务中断时保护数据库。 自动备份和高可用性等功能通过不同的恢复时间和数据丢失情况解决了不同级别的故障保护。 当你设计应用程序以防止故障时，应考虑到每个应用程序的恢复时间目标 (RTO) 和恢复点目标 (RPO) 。 RTO 是指停机容差，RPO 是数据库服务中断后的数据丢失容错。

下表说明了灵活的服务器提供的功能。


| **功能** | **描述** | **限制** |
| ---------- | ----------- | ------------ |
| **备份 & 恢复** | 灵活的服务器自动执行数据库文件的每日备份，并连续备份事务日志。 备份可以保留1到35天之间的任何时间段。 您将能够将您的数据库服务器还原到备份保持期内的任何时间点。 恢复时间将取决于要还原的数据的大小 + 执行日志恢复的时间。 有关更多详细信息，请参阅 [概念-备份和还原](./concepts-backup-restore.md) 。 |备份数据保留在区域内 |
| **本地冗余备份** | 灵活的服务器备份自动并安全地存储在区域内和同一可用性区域中的本地冗余存储中。 本地冗余备份在主要区域中的单个物理位置内将服务器备份数据文件复制三次。 本地冗余备份存储至少提供 99.999999999% (11 个 9) 对象在给定年的持续性。 有关更多详细信息，请参阅 [概念-备份和还原](./concepts-backup-restore.md) 。| 适用于所有区域 |
| **区域冗余高可用性** | 灵活的服务器可以在高可用性模式下部署，它在一个区域内的两个不同的可用性区域中部署主服务器和备用服务器。 这可以防止区域级别的故障，还有助于减少计划内和计划外停机事件期间的应用程序停机时间。 主服务器中的数据将同步复制到备用副本。 在发生任何停机事件时，数据库服务器会自动故障转移到备用副本。 有关更多详细信息，请参阅 [概念-高可用性](./concepts-high-availability.md) 。 | 在常规用途和内存优化计算层中受支持。 仅在有多个区域可用的区域中可用。|
| **高级文件共享** | 数据库文件存储在一个高度持久且可靠的 Azure 高级文件共享中，它通过自动数据恢复功能为可用性区域内存储的三个副本提供数据冗余。 有关更多详细信息，请参阅 [高级文件共享](../../storage/files/storage-how-to-create-premium-fileshare.md) 。 | 存储在可用性区域内的数据 |

> [!IMPORTANT]
> 无运行时间、RTO 和 RPO SLA 在预览期间提供。 本页提供的详细信息，仅供参考。

## <a name="planned-downtime-mitigation"></a>缓解计划内停机
下面是一些导致停机的计划内维护方案：

| **方案** | **Process**|
| :------------ | :----------- |
| **计算缩放 (用户) **| 执行计算缩放操作时，会使用缩放的计算配置来预配新的灵活服务器。 在现有的数据库服务器中，允许完成活动检查点，客户端连接将耗尽，所有未提交的事务将被取消，然后关闭。 然后，将存储连接到新服务器，启动数据库，该数据库在需要时执行恢复，然后才能接受客户端连接。 |
| **Azure)  (新的软件部署 ** | 新功能的推出或 bug 修复会自动在服务的计划内维护过程中发生，并且你可以计划这些活动发生的时间。 有关详细信息，请参阅 [文档](https://aka.ms/servicehealthpm)以及查看 [门户](https://aka.ms/servicehealthpm) |
| **Azure)  (次版本升级 ** | Azure Database for MySQL 会自动将数据库服务器修补到 Azure 确定的次要版本。 这是在服务的计划内维护过程中发生的。 这会导致短暂的停机（以秒为单位），并且会自动重启装有新次要版本的数据库服务器。 有关详细信息，请参阅 [文档](https://docs.microsoft.com/azure/mysql/concepts-monitoring#planned-maintenance-notification)，并查看 [门户](https://aka.ms/servicehealthpm)。|

当使用 **区域冗余高可用性**配置了灵活的服务器时，灵活服务器首先在备用服务器上执行操作，然后在主服务器上执行操作，而不进行故障转移。 有关更多详细信息，请参阅 [概念-高可用性](./concepts-high-availability.md) 。

##  <a name="unplanned-downtime-mitigation"></a>缓解计划外停机

由于不可预见的故障（包括基础硬件故障、网络问题和软件错误）导致了未计划的停机时间。 如果数据库服务器意外关闭（如果配置了高可用性 [HA]），则会激活备用副本。 如果不是，则自动预配新的数据库服务器。 尽管不能避免非计划的停机时间，但通过在数据库服务器和存储层上自动执行恢复操作而无需人工干预，灵活的服务器可减少停机时间。

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>计划外停机：故障场景和服务恢复

下面是一些计划外故障方案和恢复过程：

| **方案** | **恢复过程 [非 HA]** | **恢复过程 [HA]** |
| :---------- | ---------- | ------- |
| **数据库服务器故障** | 如果数据库服务器由于某些基础硬件故障而关闭，则会丢弃处于活动状态的连接，并中止任何正在进行的事务。 Azure 将尝试重新启动数据库服务器。 如果成功，则执行数据库恢复。 如果重新启动失败，则数据库服务器将尝试在另一个物理节点上重新启动。  <br /> <br /> 恢复时间 (RTO) 取决于各种因素，包括发生故障时的活动，例如，在数据库服务器启动过程中需执行的大型事务和恢复量。 <br /> <br /> 所构建的使用 MySQL 数据库的应用程序需要能够检测并重试丢弃的连接和失败的事务。  当应用程序重试时，连接将定向到新创建的数据库服务器。 | 如果检测到数据库服务器故障，则会激活备用数据库服务器，从而减少停机时间。 有关更多详细信息，请参阅 [HA 概念页](concepts-high-availability.md) 。 RTO 应为60-120 秒，RPO = 0 |
| **存储失败** | 对于任何与存储相关的问题（例如磁盘故障或物理块损坏），应用程序看不到任何影响。 由于数据存储在 3 个副本中，因此将由未发生故障的存储提供数据的副本。 块损坏会自动修复。 如果丢失了数据的副本，则会自动创建数据的新副本。 | 对于不可恢复的错误，弹性服务器会故障转移到备用副本，以减少停机时间。 有关更多详细信息，请参阅 [HA 概念页](../concepts-high-availability.md) 。 |
| **逻辑/用户错误** | 在发生用户错误（例如，意外删除了表或错误地更新了数据）后进行的恢复涉及到执行[时间点恢复](https://docs.microsoft.com/azure/MySQL/concepts-backup) (PITR)，方法是将数据还原并恢复到发生错误之前的那个时间点。<br> <br>  如果只需还原部分数据库或特定的表，而不是还原数据库服务器中的所有数据库，则可在新实例中还原数据库服务器，通过 [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) 导出表，然后使用 [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) 将这些表还原到数据库中。 | 由于将所有用户操作都复制到备用状态，因此这些用户错误不会因高可用性而受到保护。 |
| **可用性区域故障** | 虽然这是一个很少见的事件，但如果想要从区域级别的故障中恢复，则可以使用备份来执行时间点恢复，并选择自定义的还原点以访问最新数据。 新的灵活服务器将部署在另一个区域中。 还原所需的时间取决于以前的备份和要恢复的事务日志数。 | 灵活的服务器执行自动故障转移到备用站点。 有关更多详细信息，请参阅 [HA 概念页](../concepts-high-availability.md) 。 |
| **区域故障** | 预览版尚不支持跨区域副本和异地还原功能。 | |


> [!IMPORTANT]
>  **无法**   还原已删除的服务器。 如果删除服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 使用[Azure 资源锁](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)   来帮助防止意外删除服务器。


## <a name="next-steps"></a>后续步骤

-   了解 [区域冗余高可用性](./concepts-high-availability.md)
-   了解 [备份和恢复](./concepts-backup-restore.md)
