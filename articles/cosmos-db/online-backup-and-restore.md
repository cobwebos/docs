---
title: Azure Cosmos DB 中的自动联机备份和按需数据还原
description: 本文介绍了 Azure Cosmos DB 中自动联机备份和按需数据还原的工作原理。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930230"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB 中的联机备份和按需数据还原

Azure Cosmos DB 会定期自动备份数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份均单独存储在一个存储服务中，并会进行全局复制，以便在发生区域性灾难时可以复原。 如果意外删除或更新了 Azure Cosmos 帐户、数据库或容器，而稍后需要恢复数据，那么在这种情况下自动备份非常有用。

## <a name="automatic-and-online-backups"></a>自动联机备份

使用 Azure Cosmos DB，数据和数据备份都高度冗余，并且具有可复原性，能抵御区域性灾难。 以下步骤演示 Azure Cosmos DB 如何执行数据备份：

* Azure Cosmos DB 每 4 小时自动备份数据库一次，在任何时间点都只存储最新的 2 个备份。 不过，如果删除了容器或数据库，Azure Cosmos DB 会将给定容器或数据库中的现有快照保留 30 天。

* Azure Cosmos DB 将这些备份存储在 Azure Blob 存储中，而实际数据以本地形式驻留在 Azure Cosmos DB 中。

*  为了保证低延迟，备份的快照存储在 Azure Blob 存储中，且所在区域与 Azure Cosmos 数据库帐户的当前写入区域（如果有多主数据库配置，则为其中一个写入区域）相同。 对于针对区域性灾难的恢复，Azure Blob 存储中备份数据的屏幕快照将通过异地冗余存储 (GRS) 再次复制到另一区域。 根据源区域以及与源区域关联的区域对确定备份复制的目标区域。 若要了解详细信息，请参阅 [Azure 区域的异地冗余对列表](../best-practices-availability-paired-regions.md)一文。 不能直接访问此数据库。 Azure Cosmos DB 仅在启动备份还原时使用此备份。

* 备份不会影响应用程序的性能或可用性。 Azure Cosmos DB 在后台执行数据备份，不会消耗任何其他预配吞吐量 (RU)，也不会影响数据库的性能和可用性。

* 如果意外删除或损坏了数据，则应在 8 小时内联系 [Azure 支持](https://azure.microsoft.com/support/options/)，以便 Azure Cosmos DB 团队帮助你从备份中还原数据。

下图显示了如何在美国西部的远程 Azure Blob 存储帐户中备份 Azure Cosmos 容器（其三个主要物理分区全部位于美国西部），然后将其复制到美国东部：

![GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>管理自己的备份的选项

通过 Azure Cosmos DB SQL API 帐户，还可采用以下某一方法维护备份：

* 使用 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)定期将数据移至所选的存储。

* 使用 Azure Cosmos DB [更改源](change-feed.md)定期读取数据来进行完整备份和增量更改，并将其存储在自己的存储中。

## <a name="backup-retention-period"></a>备份保留期

Azure Cosmos DB 每四个小时拍摄一次数据快照。 在任何给定时间，只保留最后两个快照。 不过，如果删除了容器或数据库，Azure Cosmos DB 会将给定容器或数据库中的现有快照保留 30 天。

## <a name="restoring-data-from-online-backups"></a>从联机备份还原数据

在以下情况下可能会意外删除或修改数据：  

* 删除了整个 Azure Cosmos 帐户

* 删除了一个或多个 Azure Cosmos 数据库

* 删除了一个或多个 Azure Cosmos 容器

* 删除或修改了容器中的 Azure Cosmos 项（例如文档）。 此特定情况通常称为"数据损坏"。

* 删除或损坏了共享产品数据库中的共享产品数据库或容器

在上述所有情况中，Azure Cosmos DB 均可还原数据。 还原进程始终会创建一个新的 Azure Cosmos 帐户来保存还原数据。 如果未指定新帐户的名称，则其名称将采用 `<Azure_Cosmos_account_original_name>-restored1` 格式。 如果尝试多次还原，则最后一位数将递增。 不能将数据还原到预先创建的 Azure Cosmos 帐户中。

删除某个 Azure Cosmos 帐户后，如果该帐户名未被使用，则可将数据还原到相同名称的帐户中。 在这种情况下，建议不要在删除后重新创建帐户，因为这样不仅会阻止还原数据使用相同的名称，还会加大确定正确还原帐户的难度。 

删除 Azure Cosmos 数据库后，可以还原该数据库中的整个数据库或容器子集。 还可以跨数据库选择容器并还原它们，然后将所有还原的数据存放在新的 Azure Cosmos 帐户中。

如果意外删除或更改了容器中的一个或多个项（即数据损坏），需要指定还原到的时间。 对于这种情况，时间至关重要。 由于容器是实时的，所以备份仍在运行，因此如果超过了保持期（默认值为 8 小时），备份将被覆盖。 如果数据已被删除，则不再存储数据，因为它们不会被备份周期覆盖。 已删除数据库或容器的备份将保存 30 天。

如果在数据库级别预配吞吐量（即，一组容器共享预配吞吐量），那么在这种情况下，将对整个数据库，而不是单个容器进行备份和还原。 在这种情况下，不可选择还原容器子集。

## <a name="migrating-data-to-the-original-account"></a>将数据迁移到原始帐户

数据还原的主要目标是提供一种方法来恢复意外删除或修改的任何数据。 因此，建议先检查已还原数据，确保其中包含所需内容。 然后再将数据迁移回主帐户。 尽管可以使用已还原帐户作为实时帐户，但如果有生产工作负载则不建议这样做。  

可通过以下方式将数据迁移回原始的 Azure Cosmos 帐户：

* 使用 [Cosmos DB 数据迁移工具](import-data.md)
* 使用 [Azure 数据工厂]( ../data-factory/connector-azure-cosmos-db.md)
* 使用 Azure Cosmos DB 中的[更改源](change-feed.md) 
* 编写自定义代码

迁移完成后立即删除已还原帐户，因为它们会持续产生费用。

## <a name="next-steps"></a>后续步骤

接下来可了解如何从 Azure Cosmos 帐户还原数据，或了解如何将数据迁移到 Azure Cosmos 帐户

* 若要提出还原请求，请联系 Azure 支持，并[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [如何从 Azure Cosmos 帐户还原数据](how-to-backup-and-restore.md)
* [使用 Cosmos DB 更改源](change-feed.md)将数据移动到 Azure Cosmos DB。
* [使用 Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)将数据移动到 Azure Cosmos DB。

