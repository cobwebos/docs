---
title: Azure Cosmos DB 的联机备份和还原 | Microsoft Docs
description: 了解如何在 Azure Cosmos DB 数据库上执行自动备份和还原。
keywords: 备份和还原、联机备份
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 66b4f63e75773aa0c1857dfcc19e22b48a0c3537
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343141"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Azure Cosmos DB 的自动联机备份和还原
Azure Cosmos DB 会定期自动备份所有数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份将存储在另一个存储服务中，针对区域灾难会对这些备份进行全局复制。 如果意外删除了 Cosmos DB 容器并且之后需要数据恢复或灾难恢复解决方案，那么自动备份将是合适的方案。  

本文将首先简要回顾 Cosmos DB 中的数据冗余性和可用性，然后再开始讨论备份。 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Cosmos DB 的高可用性 - 概要
Cosmos DB 旨在进行[全球分布](distribute-data-globally.md) - 允许跨多个 Azure 区域以及策略驱动的故障转移和透明多宿主 API 调整吞吐量规模。 Azure Cosmos DB 为所有单区域帐户和具有松散一致性的所有多区域帐户提供 [99.99% 的可用性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)，为所有多区域数据库帐户提供 99.999% 的读取可用性。 Azure Cosmos DB 中的所有写入在确认到客户端之前，都会通过本地数据中心内的副本仲裁持久提交到本地磁盘。 Cosmos DB 的高可用性依赖本地存储，而不依赖任何外部存储技术。 此外，如果数据库帐户与多个 Azure 区域关联，那么还将跨其他区域复制写入。 若要调整吞吐量规模并以低延迟访问数据，可以拥有任意数量的与数据库帐户关联的读取区域。 在每个读取区域中，（复制）数据会在副本集中永久保存。  

如下面的关系图中所示，单个 Cosmos DB 容器将[水平分区](partition-data.md)。 在下图中，圆圈表示“分区”，每个分区通过副本集而具有高度可用性。 这是单个 Azure 区域（X 轴表示）内的本地分布。 此外，每个分区（含有其相应的副本集）都会在与数据库帐户关联的多个区域中进行全球分布（例如，此图中的三个区域 - 美国东部、美国西部和印度中部）。 “分区集”是一个包含每个区域（Y 轴表示）中多个数据副本的全球式分布实体。 可将优先级分配到与数据库帐户关联的区域，Cosmos DB 将透明故障转移到下一区域，以防灾难发生。 也可以手动模拟故障转移以测试应用程序的端到端可用性。  

下图阐释了 Cosmos DB 的高程度冗余性。

![Cosmos DB 的高程度冗余性](./media/online-backup-and-restore/redundancy.png)

![Cosmos DB 的高程度冗余性](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>完整、自动、联机备份
糟糕，我删除了容器或数据库！ 使用 Cosmos DB，不仅是数据，还有数据的备份都会高度冗余和具可恢复性，来应对区域灾难。 这些自动备份当前大约每四小时进行一次，并且始终存储最新的两个备份。 如果数据意外删除或损坏，请在八小时内[联系 Azure 支持部门](https://azure.microsoft.com/support/options/)。 

备份不会影响数据库操作的性能或可用性。 Cosmos DB 会在后台进行备份，不会使用预配的 RU 或影响性能，也不会影响数据库的可用性。 

与存储在 Cosmos DB 中的数据不同，自动备份存储在 Azure Blob 存储服务中。 要保证低延迟/高效上传，备份的屏幕快照将上传到位于与 Cosmos DB 数据库帐户的当前写入区域相同的区域中的 Azure Blob 存储实例。 对于针对区域灾难的恢复，Azure Blob 存储中备份数据的屏幕快照将通过异地冗余存储 (GRS) 再次复制到另一区域。 下图显示在美国西部的远程 Azure Blob 存储帐户中备份整个 Cosmos DB 容器（在此示例中，三个主分区全部位于美国西部），然后将 GRS 复制到美国东部。 

下图阐释了 GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份。

![GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>备份保留期
如上所述，Azure Cosmos DB 在分区级别每四小时创建一次数据快照。 在任何给定时间，只保留最后两个快照。 不过，如果删除了容器/数据库，Azure Cosmos DB 将给定容器/数据库中所有已删除分区的现有快照保留 30 天。

对于 SQL API，若要保留自己的快照，可以使用 Azure Cosmos DB [数据迁移工具](import-data.md#export-to-json-file)中的“导出到 JSON”选项，计划其他备份。

> [!NOTE]
> 如果“在数据库级别为一组容器预配吞吐量”，请记得在整个数据库帐户级别进行还原操作。 如果无意中删除了容器，还需要确保在 8 小时内联系支持团队。 如果未在 8 小时内联系支持团队，则无法还原数据。 


## <a name="restoring-a-database-from-an-online-backup"></a>从联机备份还原数据库

如果意外删除了数据库或容器，可以[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)或[联系 Azure 支持](https://azure.microsoft.com/support/options/)，以便从上一次自动备份中还原数据。 Azure 支持仅适用于选定计划（例如标准计划、发开人员计划），不适用于基本计划。 若要了解不同的支持方案，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)页。 

如果由于数据损坏（包括删除了容器中的文档）而需要还原数据库，请参阅[处理数据损坏](#handling-data-corruption)，因为需要采取额外步骤，防止损坏的数据覆盖现有备份。 对于要还原备份的特定快照，Cosmos DB 要求数据在该快照的备份周期的持续时间内可用。

## <a name="handling-data-corruption"></a>处理数据损坏

Azure Cosmos DB 保留数据库帐户中每个分区的最后两个备份。 当意外删除了容器（文档、图、表的集合）或数据库时，此模型适用，因为它可以还原其中一个最新版本。 但是，在用户可能引入数据损坏问题的情况下，Azure Cosmos DB 可能不知道数据损坏，并且损坏可能已覆盖现有备份。 

一旦检测到损坏，请联系客户支持，并提供数据库帐户和容器信息（内含出现损坏的大致时间）。 出现损坏（数据删除/更新）时用户可以执行的另一项操作是，应删除损坏的容器（集合/图/表），以防备份被损坏的数据覆盖。  

下图展示了如何通过 Azure 门户创建支持请求，以便在意外删除或更新容器中的数据时还原容器（集合/图/表）

![因在 Cosmos DB 中错误升级或删除数据而还原容器](./media/online-backup-and-restore/backup-restore-support.png)

针对这种情况完成还原后，数据会还原到其他帐户（后缀为“-restored”）和容器中。 完成此还原并不是为了让客户有机会根据需要验证数据和移动数据。 还原的容器与相同的 RU 和索引策略位于同一区域中。 

## <a name="next-steps"></a>后续步骤

若要在多个数据中心中复制数据库，请参阅[使用 Cosmos DB 对数据进行全球分布](distribute-data-globally.md)。 

若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

