---
title: Azure Cosmos DB 中的联机备份和按需数据还原
description: 本文介绍了自动备份、按需数据还原的工作原理，以及如何在 Azure Cosmos DB 中配置备份时间间隔和保留期。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 54bbd5d45e14c1d345570eea9dc5469f77694154
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853921"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB 中的联机备份和按需数据还原

Azure Cosmos DB 会定期自动备份数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份均单独存储在一个存储服务中，并会进行全局复制，以便在发生区域性灾难时可以复原。 如果意外删除或更新了 Azure Cosmos 帐户、数据库或容器，而稍后需要恢复数据，那么在这种情况下自动备份非常有用。

## <a name="automatic-and-online-backups"></a>自动联机备份

使用 Azure Cosmos DB，数据和数据备份都高度冗余，并且具有可复原性，能抵御区域性灾难。 以下步骤演示 Azure Cosmos DB 如何执行数据备份：

* 默认情况下，Azure Cosmos DB 每4小时自动备份一次数据库，在任何时间点都只存储最新的两个备份。 如果默认间隔不能满足工作负荷要求，则可以更改 Azure 门户的备份间隔和保持期。 你可以在创建 Azure Cosmos 帐户期间或之后更改备份配置。 如果删除了容器或数据库，Azure Cosmos DB 会将给定容器或数据库的现有快照保留30天。

* Azure Cosmos DB 将这些备份存储在 Azure Blob 存储中，而实际数据以本地形式驻留在 Azure Cosmos DB 中。

* 为了保证低延迟，你的备份快照存储在与当前写入区域相同的区域中的 Azure Blob 存储中 (或 **其中一个** 写入区域，以防有多主机配置) 。 对于针对区域性灾难的恢复，Azure Blob 存储中备份数据的屏幕快照将通过异地冗余存储 (GRS) 再次复制到另一区域。 根据源区域以及与源区域关联的区域对确定备份复制的目标区域。 若要了解详细信息，请参阅 [Azure 区域的异地冗余对列表](../best-practices-availability-paired-regions.md)一文。 不能直接访问此数据库。 Azure Cosmos DB 团队将在你请求通过支持请求时还原备份。

   下图显示了如何在美国西部的远程 Azure Blob 存储帐户中备份 Azure Cosmos 容器（其三个主要物理分区全部位于美国西部），然后将其复制到美国东部：

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份" border="false":::

* 备份不会影响应用程序的性能或可用性。 Azure Cosmos DB 在后台执行数据备份，不会消耗任何其他预配吞吐量 (RU)，也不会影响数据库的性能和可用性。

## <a name="options-to-manage-your-own-backups"></a>管理自己的备份的选项

通过 Azure Cosmos DB SQL API 帐户，还可采用以下某一方法维护备份：

* 使用 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)定期将数据移至所选的存储。

* 使用 Azure Cosmos DB [更改源](change-feed.md) 来定期读取数据以进行完整备份或增量更改，并将其存储在自己的存储中。

## <a name="modify-the-backup-interval-and-retention-period"></a>修改备份间隔和保持期

Azure Cosmos DB 每隔4小时自动备份一次数据，在任何时间点都存储最新的两个备份。 此配置是默认选项，提供它时不需要任何额外的费用。 你可以在创建 Azure Cosmos 帐户期间或创建帐户后更改默认备份间隔和保持期。 备份配置在 Azure Cosmos 帐户级别设置，需要在每个帐户上进行配置。 为帐户配置备份选项后，该帐户将应用到该帐户中的所有容器。 目前只能从 Azure 门户更改它们的备份选项。

如果你意外删除或损坏了数据， **则在创建支持请求以还原数据之前，请确保将你的帐户的备份保留期至少增加了7天。最好在此事件的8小时内提高你的保留期。** 这样，Azure Cosmos DB 团队有足够的时间来还原你的帐户。

使用以下步骤更改现有 Azure Cosmos 帐户的默认备份选项：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到你的 Azure Cosmos 帐户，并打开 **备份 & 还原** 窗格。 根据需要更新备份间隔和备份保留期。

   * **备份间隔** -Azure Cosmos DB 尝试对数据进行备份的时间间隔。 备份需要非零时间，在某些情况下，可能由于下游依赖关系而失败。 Azure Cosmos DB 尝试在配置的时间间隔内进行备份，但它不能保证备份在该时间间隔内完成。 可以用小时数或分钟数来配置此值。 备份间隔不能小于1小时且不能超过24小时。 更改此间隔后，新时间间隔将从上次备份的时间开始生效。

   * **备份保留** 期-它表示保留每个备份的时间段。 你可以在数小时或数天内配置。 最小保持期不能小于备份间隔 (的两倍) 小时，并且不能超过720小时。

   * **保留的数据副本** -默认情况下，会免费提供数据的两个备份副本。 如果需要额外的副本，则必须通过 Azure 门户创建支持请求，并将对其他副本收费。 请参阅 [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/) 中的 "已用存储" 部分，了解其他副本的确切价格。

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="为现有的 Azure Cosmos 帐户配置备份间隔和保留期" border="true":::

如果在帐户创建过程中配置备份选项，则可以配置 **备份策略**，该策略可能是 **定期** 或 **连续**的。 定期策略用于配置备份间隔和备份保留期。 持续策略目前仅通过注册提供。 Azure Cosmos DB 团队将评估你的工作负荷并批准你的请求。

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="为新的 Azure Cosmos 帐户配置定期或连续备份策略" border="true":::

## <a name="restore-data-from-an-online-backup"></a>从联机备份还原数据

在以下情况下，可能会意外删除或修改数据：  

* 删除整个 Azure Cosmos 帐户。

* 删除一个或多个 Azure Cosmos 数据库。

* 删除一个或多个 Azure Cosmos 容器。

* 删除或修改 Azure Cosmos 项 (例如，文档) 容器中。 这种特定情况通常称为 "数据损坏"。

* 共享的服务数据库中的共享产品/服务已删除或已损坏。

在上述所有情况中，Azure Cosmos DB 均可还原数据。 在还原时，将创建一个新的 Azure Cosmos 帐户来保存已还原的数据。 如果未指定，则新帐户的名称将采用格式 `<Azure_Cosmos_account_original_name>-restored1` 。 尝试多次还原时，最后一个数字会递增。 不能将数据还原到预先创建的 Azure Cosmos 帐户中。

如果意外删除了 Azure Cosmos 帐户，可以将数据还原到同名的新帐户，前提是该帐户名称未被使用。 因此，建议您不要在删除帐户后重新创建它。 因为它不仅会阻止已还原的数据使用相同的名称，而且还会使发现正确的帐户从难以还原。

如果意外删除了 Azure Cosmos 数据库，可以还原整个数据库或该数据库中的一部分容器。 还可以在数据库中选择特定容器，并将其还原到新的 Azure Cosmos 帐户。

如果意外删除或修改容器中的一个或多个项 (数据损坏事例) ，则需要指定还原到的时间。 如果数据损坏，时间很重要。 由于容器处于活动状态，因此备份仍在运行，因此，如果你等待超过保持期 (默认值为8小时) 将覆盖备份。 **若要防止备份被覆盖，请将帐户的备份保留至少增加7天。最好在8小时内提高数据损坏的保留期。**

如果意外删除或损坏了数据，则应在 8 小时内联系 [Azure 支持](https://azure.microsoft.com/support/options/)，以便 Azure Cosmos DB 团队帮助你从备份中还原数据。 这样，Azure Cosmos DB 支持团队将有足够的时间来还原你的帐户。

如果在数据库级别预配吞吐量，则在这种情况下，备份和还原过程将在整个数据库级别发生，而不是在单独的容器级别进行。 在这种情况下，不能选择要还原的容器的子集。

## <a name="migrate-data-to-the-original-account"></a>将数据迁移到原始帐户

数据还原的主要目的是恢复意外删除或修改的数据。 因此，建议先检查已还原数据，确保其中包含所需内容。 稍后，你可以将数据迁移回主帐户。 尽管可以使用还原的帐户作为新的活动帐户，但如果有生产工作负荷，建议使用此选项。  

可通过以下方式将数据迁移回原始的 Azure Cosmos 帐户：

* 使用 [Azure Cosmos DB 数据迁移工具](import-data.md)。
* 使用 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)。
* 使用 Azure Cosmos DB 中的 [更改源](change-feed.md) 。
* 您可以编写自己的自定义代码。

请确保在迁移数据后立即删除已还原的帐户，因为这些帐户将产生持续的费用。

## <a name="next-steps"></a>后续步骤

接下来可了解如何从 Azure Cosmos 帐户还原数据，或了解如何将数据迁移到 Azure Cosmos 帐户

* 若要提出还原请求，请联系 Azure 支持，并[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [如何从 Azure Cosmos 帐户还原数据](how-to-backup-and-restore.md)
* [使用 Cosmos DB 更改源](change-feed.md)将数据移动到 Azure Cosmos DB。
* [使用 Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)将数据移动到 Azure Cosmos DB。

